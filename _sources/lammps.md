# LAMMPS Tutorials


### Typical input files required (The names can be changed)

- in.lammps: Input file. This file has all the parameters controlling the simulations (e.g. Thermostat, Barostate, Ensemble) 
- geom.xyz: (optional) Geometry file. This file describes the coordinates of your simulation system. Alternatively, you can also define your system geometry in the input file.
- potential file: (optional) A file defining the MD potential that you are using.

### LAMMPS installation
Create a folder named software in your adroit home directory (/home/Your_princeton_ID).

```sh
cd /home/Your_princeton_ID
mkdir software
```

Change the directory to the software folder
```sh
cd ./software
```

This is the lammps installation script. Copy the contents of the following code block to the
software folder with a file names 'install.sh'. (Alternatively I have uploaded this file on 
Canvas in the folder LAMMPS tutorial1).


```sh
#!/bin/bash

# double-precision build for single- and multi-node CPU jobs

VERSION=31Aug2021
wget https://github.com/lammps/lammps/archive/refs/tags/patch_${VERSION}.tar.gz
tar zvxf patch_${VERSION}.tar.gz
cd lammps-patch_${VERSION}
mkdir build && cd build

# include the modules below in your Slurm scipt
module purge
module load intel/19.1.1.217 intel-mpi/intel/2019.7

cmake3 \
-D CMAKE_INSTALL_PREFIX=$HOME/.local \
-D LAMMPS_MACHINE=adroit \
-D CMAKE_BUILD_TYPE=Release \
-D CMAKE_CXX_COMPILER=icpc \
-D CMAKE_CXX_FLAGS_RELEASE="-Ofast -xHost -DNDEBUG" \
-D CMAKE_Fortran_COMPILER=/opt/intel/compilers_and_libraries_2020.1.217/linux/bin/intel64/ifort \
-D BUILD_OMP=yes \
-D BUILD_MPI=yes \
-D PKG_KSPACE=yes -D FFT=MKL -D FFT_SINGLE=no \
-D PKG_OPENMP=yes \
-D PKG_MOLECULE=yes \
-D PKG_RIGID=yes \
-D PKG_REAXFF=yes\
-D ENABLE_TESTING=yes ../cmake

make -j 10
#make test
make install

```

Make the script executable by using the following command

```sh
chmod +x ./install.sh
```

Then run the installations using the following command. Make sure you open the visualization node
on Adroit dashboard as that has internet access required for installation. 

```sh
./install.sh
```



Use the following job submission script to submit a LAMMPS job. Copy the following content to a file named
'job.slurm' on your scratch folder (/scratch/network/al9001/). Make sure you replace my princeton Id with yours. 

Alternatively the script is also available on Canvas.

```sh
#!/bin/bash
#SBATCH --job-name=lj-melt       # create a short name for your job
#SBATCH --nodes=1                # node count
#SBATCH --ntasks=4               # total number of tasks across all nodes
#SBATCH --cpus-per-task=1        # cpu-cores per task (>1 if multi-threaded tasks)
#SBATCH --mem-per-cpu=1G         # memory per cpu-core (4G is default)
#SBATCH --time=00:05:00          # total run time limit (HH:MM:SS)
#SBATCH --mail-type=begin        # send email when job begins
#SBATCH --mail-type=end          # send email when job ends
#SBATCH --mail-user=<YourNetID>@princeton.edu

module purge
module load intel/19.1.1.217 intel-mpi/intel/2019.7
export OMP_NUM_THREADS=$SLURM_CPUS_PER_TASK

srun /home/al9001/.local/bin/lmp_adroit -in in.lammps
```
Then create an input file in the same location with the name in.lammps. The input file can be copied from below or dowloaded from Canvas.

```sh
units           real
atom_style      atomic


read_data       ar.data

velocity        all create 1.0 87287

pair_style      lj/cut 2.5
pair_coeff      1 1 1.0 1.0 2.5

neighbor        0.3 bin
neigh_modify    every 20 delay 0 check no

fix             1 all npt temp 95.0 95.0 100.0 iso 1.0 1.0 1000.0




timestep        0.25

thermo          100
thermo_style    custom step pe ke etotal temp press vol density
thermo_modify   format float %15.14g

dump             1 all custom 500 dump.ar id type x y z


run             500000
```

Now, you should have 3 different files in your scratch folder.1)in.lammps 2)ar.data 3)job.slurm

Submit your lammps job using the following command

```sh
sbatch job.slurm
```

You can check your job status by using following command

```sh
squeue -u Your_Princeton_ID
```

Your job should finish in a few minutes. Once it finishes, you should be able to see a file named log.lammps. I have also 
uploaded a sample output on Canvas. 

Now you can use the gnuplot tool to plot the results

To start gnuplot type
```sh
gnuplot
```

This will take you to an interactive GNUPLOT session. Type the following command to plot density as a function of time.

```sh
plot "log.lammps" using 1:8
```

To take an average average of the density use following set of command line by line
```sh
f(x) = m * x + q 
fit [100000:500000][0:0.01] f(x) 'log.lammps' using 1:8 via m, q
plot [100000:500000][0:0.01] "log.lammps" using 1:8, f(x)
```
You should be able to see an output similar to the one that we saw in the class. 

To exit the GNUPLOT environment:
```sh
exit
```

## LAMMPS energy minimization

We will use ReaxFF example files to run an energy minimization simulation. Copy the ReaxFF CHO exmaple folder 

```sh
cp -r \location_of_lammps_installation\examples\reaxff\CHO \scratch\network\<your_net_ID>\min
```

Now we will make some changes to the input file to run energy minimization. You can copy the following to your input file

```sh
units           real

atom_style      charge
read_data      CH4.lammps-data
#read_data       mix.data

pair_style      reax/c lmp_control
pair_coeff      * * ffield.reax.cho C H

neighbor        2 bin
neigh_modify    every 10 delay 0 check no

#fix            1 all nve
#fix             1 all nvt temp 500.0 500.0 100.0
fix             2 all qeq/reax 1 0.0 10.0 1e-6 param.qeq
#fix             3 all temp/berendsen 500.0 500.0 100.0

#timestep       0.25
#run             10000

#dump           1 all atom 1 dump.reax.cho
dump             1 all custom 1 dump.cho id element x y z
dump_modify     1 element C H
minimize        1.0e-5 1.0e-6 100 100000
min_style       cg
#run            3000
```

Also, generate a data file with following coordinates.

```sh
# LAMMPS data file written by OVITO Basic 3.5.4
5 atoms
2 atom types
-0.5676 6.63 xlo xhi
-0.7276 6.63 ylo yhi
-0.7276 6.63 zlo zhi

Masses

1 12.0107  # C
2 1.00794  # H

Atoms  # charge

1 1 0.0 0.0 0.0 0.0
2 2 0.0 0.63 0.63 0.63
3 2 0.0 0.5276 -0.6276 -0.7276
4 2 0.0 -0.4276 0.4276 -0.5276
5 2 0.0 -0.5676 -0.7276 0.6276
```

Copy and edit the job submission script to submit the job.

## LAMMPS diffusion example

```sh
#!/bin/bash
#SBATCH --job-name=diff       # create a short name for your job
#SBATCH --nodes=1                # node count
#SBATCH --ntasks=4               # total number of tasks across all nodes
#SBATCH --cpus-per-task=1        # cpu-cores per task (>1 if multi-threaded tasks)
#SBATCH --mem-per-cpu=1G         # memory per cpu-core (4G is default)
#SBATCH --time=00:15:00          # total run time limit (HH:MM:SS)
#SBATCH --mail-type=begin        # send email when job begins
#SBATCH --mail-type=end          # send email when job ends
#SBATCH --mail-user=al9001@princeton.edu

module purge
module load intel/19.1.1.217
module load intel-mpi/intel/2019.7
export OMP_NUM_THREADS=$SLURM_CPUS_PER_TASK
###Create directories
mkdir {2000..10000..2000}

###Copy files into the directories

#for d in ./*/; do cp limn2o4.vac.data limn2o4.msd.in "$d"; done


for d in */ ; do
    d2=${d::-1}
#    echo "$d" &> ./"$d"/test.txt
     echo "units           metal

dimension       3
boundary        p p p
atom_style      atomic
neighbor        2.5 bin
neigh_modify    every 1


read_data       limn2o4.vac.data
pair_style      lj/cut 16.5
pair_coeff      1 1 0.249 3.57 16.5
pair_coeff      1 2 0.292 3.25 16.5
pair_coeff      1 3 0.195 1.77 16.5
pair_coeff      2 2 0.204 2.91 16.5
pair_coeff      2 3 0.226 2.06 12.0
pair_coeff      3 3 0.204 2.91 12.0

timestep        0.0005
fix             1 all nvt temp "$d2" "$d2" 0.05
velocity        all create 10000 9865758 dist uniform

dump            1 all xyz 1000 limn2o4.xyz
dump_modify     1 element Li Mn O

group           lithiums type 1
group           manganeses type 2
group           oxygens type 2
compute         mymsdli lithiums msd com yes 
compute         mymsdmn manganeses msd com yes 
compute         mymsdo oxygens msd com yes 

thermo          1000
thermo_style    custom step time temp c_mymsdli[4] c_mymsdmn[4] c_mymsdo[4]

run             100000" &> ./"$d"/limn2o4.msd.in

   echo "Structure of LiMn2O4
 
      52    atoms
 
         3    atom types
 
       0.000000      8.2500000 xlo xhi
       0.000000      8.2500000 ylo yhi
       0.000000      8.2500000 zlo zhi
 
              Masses
 
         1            6.941
         2           54.938
         3           15.999
               Atoms

 1 1 4.11085 4.10814 0.00177939         
 2 1 8.23585 4.10814 4.12677
 3 1 4.11085 8.23315 4.12677
 4 1 6.17335 2.04564 6.18928
 5 2 1.0171 1.0144 5.15803
 6 2 5.14211 5.13939 5.15802
 7 2 7.2046 3.07689 1.03302
 8 2 3.0796 5.1394 3.09552
 9 2 3.07961 1.01439 7.22052
10 2 5.14211 7.20189 7.22052
11 2 1.0171 7.2019 3.09552
12 2 5.1421 1.01438 1.03302
13 2 3.0796 3.0769 5.15802
14 2 7.2046 5.13938 7.22052
15 2 1.0171 5.13939 1.03302
16 2 5.14209 3.07689 3.09552
17 2 7.20461 7.20189 5.15802
18 2 3.0796 7.20189 1.03302
19 2 1.01711 3.0769 7.22052
20 2 7.2046 1.01439 3.09553
21 3 1.02723 1.02452 3.08539
22 3 7.19448 3.06676 7.2104
23 3 3.06948 5.14952 5.16815
24 3 5.15223 7.19176 1.04315
25 3 3.06948 1.02452 1.04315
26 3 7.19447 7.19176 3.0854
27 3 5.15224 3.06676 5.16815
28 3 1.02724 5.14952 7.21039
29 3 1.02723 3.06676 1.04315
30 3 3.06948 7.19176 7.2104
31 3 3.06947 3.06676 3.08539
32 3 5.15223 5.14952 3.08539
33 3 7.19448 1.02452 5.16816
34 3 7.21473 3.08702 3.10565
35 3 7.21473 7.21201 7.23065
36 3 1.00698 1.00426 7.23065
37 3 3.08973 5.12926 1.0229
38 3 5.13198 7.21202 5.1479
39 3 7.21473 5.12926 5.14789
40 3 5.13197 1.00427 3.10565
41 3 1.00697 7.21202 1.0229
42 3 3.08974 3.08702 7.23065
43 3 1.00698 3.08702 5.1479
44 3 5.13198 5.12926 7.23065
45 3 3.08973 7.21202 3.10565
46 3 7.21473 1.00426 1.0229
47 3 5.13197 3.08701 1.0229
48 3 1.00697 5.12927 3.10565
49 3 3.08973 1.00427 5.1479
50 3 7.19447 5.14952 1.04315
51 3 5.15224 1.02451 7.2104
52 3 1.02724 7.19177 5.16815       " &> ./"$d"/limn2o4.vac.data

   cd "$d"
   srun ~/.local/bin/lmp_adroit -in limn2o4.msd.in
   cd ../
done
```

Copy the following content to a file named run.sh. 

```sh
#!/bin/bash
for d in */ ; do
   cd "$d"
   /usr/bin/gnuplot -e 'set term png' -e "plot 'log.lammps' u 1:4 with lines" > ./out.png
   cd ../
done
```

Make the run.sh file executable by using following command

```sh
chmod +x run.sh
```
Then run it after the job is run.

## OVITO

[OVITO](https://www.ovito.org/) is an easy to use visualization software that can also be used to convert different geometry file formats. 
You can download and install the free version locally on your computer for visualization. You can download the dump.ar file from your 
simulation folder to your computer and open it using OVITO.



