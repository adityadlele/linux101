Exercise 0
==========

**Aim**
1. Make sure that you have installed all the tools properly.
2. You can access the computer cluster
3. You can perform basic tasks on the computer cluster

## Part 1
1. Go to your *network* directory and create a directory called *ex0*
2. Create the following subdirectories inside the *ex0* directory: lammps,ase

You will excute small simulations in each of the above subdirectories
## Part 2: ASE
1. Copy the following code to a file named test.py
```
from ase.spacegroup import crystal
from ase.visualize import view
from ase.build import surface
from ase import Atoms
import numpy as np
from ase.io import read, write

gAl2O3 = crystal(['Al','Al','Al',
                 'Al','Al','Al',
                 'O','O','O','O',
                 'O','O','O','O'],
                 basis=[(0.377,3./4.,0.126),(0.868,1./4.,0.498),(0.875,3./4.,0.125),(0.615,3./4.,0.745),(0.367,0.075,0.612),(0.116,0.579,0.862),(0.881,3./4.,0.874),(0.614,1./4.,0.640),(0.364,3./4.,0.889),(0.132,1./4.,0.627),(0.889,0.406,0.899),(0.605,0.917,0.614),(0.357,0.406,0.853),(0.138,0.916,0.637)],
                 spacegroup=11,
                 cellpar=[5.577, 8.401, 8.069, 90, 90.59, 90])
gAl2O3_110 = surface(gAl2O3, (1, 0, 0), 2)

gAl2O3_110 = gAl2O3_110.repeat((4,2,1))

del gAl2O3_110[gAl2O3_110.positions[:, 2] > 8.9]

write('al2o3.xyz',gAl2O3_110)
```
2. Activate the python environment using following two commands
	* `module load anaconda3/2021.11`
	* `conda activate ase`
```{note}
You will have to perform the two steps above every time you want to excute a python script
```
3. Execute the file using the following command inside the folder ase  

`python test.py`

4. Download the *al2o321.xyz* file and visualize it using OVITO. (You can access your files using files tab [here]( https://myadroit.princeton.edu))

Create an image using OVITO showing the top and two side views of the structure in the downloaded file.

## Part 3: LAMMPS
1. Copy the following code to a file named in.lammps
```sh
units           lj
atom_style      atomic

lattice         fcc 0.8442
region          box block 0 30 0 30 0 30
create_box      1 box
create_atoms    1 box
mass            1 1.0

velocity        all create 1.0 87287

pair_style      lj/cut 2.5
pair_coeff      1 1 1.0 1.0 2.5

neighbor        0.3 bin
neigh_modify    every 20 delay 0 check no

fix             1 all nve
fix             2 all langevin 1.0 1.0 1.0 48279

timestep        0.005

thermo          500
run             10000
```

2. Use the following text to make a file named *job.slurm*

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
module load intel/19.1.1.217
module load intel-mpi/intel/2019.7
export OMP_NUM_THREADS=$SLURM_CPUS_PER_TASK

srun /home/al9001/.local/bin/lmp_adroit -in in.lammps
```
3. Excecute the job using the command

`sbatch job.slurm`

You can take a look at [this](https://researchcomputing.princeton.edu/support/knowledge-base/slurm) guide to learn more about job monitoring.

4. Plot the energy as a function of time using [gnuplot](http://www.gnuplot.info/) or Python. You can use log.lammps file.

Some useful commands
```sh
gnuplot
plot "log.lammps" using 1:8
```

```sh
f(x) = m * x + q
fit [100000:500000][0:0.01] f(x) 'log.lammps' using 1:8 via m, q
plot [100000:500000][0:0.01] "log.lammps" using 1:8, f(x)
```
