# DeePMD installation
One of the easy ways to install DeePMD-kit is to install it through Conda. The DeePMD-kit website has detailed 
[instrutions](https://github.com/deepmodeling/deepmd-kit/blob/master/doc/install/easy-install.md#install-with-conda) on installation. 
Following instructions are a summary of those instructions suitable for Princeton Della cluster.


To install the cpu version of DeePMD-kit
```sh
module load anaconda3/2020.11
conda create -n deepmd deepmd-kit=*=*cpu libdeepmd=*=*cpu lammps -c https://conda.deepmodeling.com -c defaults
```

The above commands would create a python environment called deepmd where deepmd will be installed. To run the DeePMD training 
you can refer to following job submission script. 

```sh
#!/bin/sh
#SBATCH --job-name=train
#SBATCH --constraint=cascade
#SBATCH -N 1 -n 4
#SBATCH -t 23:59:00
#SBATCH --mem-per-cpu=1G
#SBATCH --mail-type=all          # send email on job start, end and fail
#SBATCH --mail-user=<your USER-ID>@princeton.edu

module load anaconda3/2020.11
conda activate deepmd
export OMP_NUM_THREADS=2
export TF_INTRA_OP_PARALLELISM_THREADS=2
export TF_INTER_OP_PARALLELISM_THREADS=2
dp train input.json ##input.json is the input file
``` 

To use the deep potential trained using DeePMD-kit with lammps use the following lammps input file and 
job submission file as a reference


###Sample LAMMPS input file
```sh

units           metal
boundary        p p p
atom_style	atomic

neighbor        2.0 bin
neigh_modify    every 10 delay 0 check no

read_data	h2o2.data##Your own data file
mass            1 2
mass            2 16

# load the deepmd plugin
plugin load libdeepmd_lmp.so

pair_style	deepmd test.pb###.pb file is the deep potential file
pair_coeff  * *

velocity        all create 330.0 23456789

fix             1 all nvt temp 330.0 330.0 0.5
timestep        0.0005
thermo_style    custom step pe ke etotal temp press vol
thermo          100
dump            1 all custom 100 water.dump id type x y z

run             1000
```

### Sample job submission script
We will use lammps that is installed as a part of DeePMD-kit installation

```sh
#!/bin/bash
#SBATCH --job-name=dmd_test                     # create a short name for your job
#SBATCH --nodes=1                                # node count
#SBATCH --ntasks=4                              # total number of tasks across all nodes
#SBATCH --cpus-per-task=1                        # cpu-cores per task (>1 if multi-threaded tasks)
#SBATCH --time=00:59:00                          # total run time limit (HH:MM:SS)
#SBATCH --mail-type=begin                        # send email when job begins
#SBATCH --mail-type=end                          # send email when job ends
#SBATCH --mail-type=fail                         # send email if job fails
#SBATCH --mail-user=<Your USER-ID>@princeton.edu
#SBATCH --dependency=singleton
#SBATCH --constraint=cascade,skylake 

module purge

module load anaconda3/2020.11
conda activate deepmd

export OMP_NUM_THREADS=$SLURM_CPUS_PER_TASK
export TF_INTRA_OP_PARALLELISM_THREADS=2
export TF_INTER_OP_PARALLELISM_THREADS=2

srun lmp -in in.deepmd
```

