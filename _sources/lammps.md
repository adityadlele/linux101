# LAMMPS Tutorial 1


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
fix             1 all nvt temp 500.0 500.0 100.0
fix             2 all qeq/reax 1 0.0 10.0 1e-6 param.qeq
#fix             3 all temp/berendsen 500.0 500.0 100.0

#timestep       0.25
run             10000

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

## OVITO

[OVITO](https://www.ovito.org/) is an easy to use visualization software that can also be used to convert different geometry file formats. 
You can download and install the free version locally on your computer for visualization. You can download the dump.ar file from your 
simulation folder to your computer and open it using OVITO.



