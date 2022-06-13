# VASP

## Reiterating some basics 

Files used to run VASP calculations
- INCAR: This file describes the input parameters required for different calculations (For example: A molecule will have different parameters than a crystal)
- KPOINTS: Description of receprocal space
- POSCAR: Define the geometry of your system
- POTCAR: Concatenated pseudopotentials for elements of your system 

## Running VASP on Della

We will try to use a VASP executable that has been already compiled

Use the following job submission script to submit a VASP job
```sh
#!/bin/bash
#SBATCH --job-name=vasp          # create a short name for your job
#SBATCH --nodes=1                # node count
#SBATCH --ntasks-per-node=8      # total number of tasks per node
#SBATCH --cpus-per-task=2        # cpu-cores per task (>1 if multi-threaded tasks)
#SBATCH --mem-per-cpu=4G         # memory per cpu-core (4G is default)
#SBATCH --time=00:01:00          # total run time limit (HH:MM:SS)
#SBATCH --mail-type=begin        # send email when job begins
#SBATCH --mail-type=end          # send email when job ends
#SBATCH --mail-user=<YourNetID>@princeton.edu

export OMP_NUM_THREADS=$SLURM_CPUS_PER_TASK
export PATH=$PATH:/home/al9001/software/vasp.6.2.1/bin/

module purge
module load intel/2021.1.2 intel-mpi/intel/2021.1.1

srun vasp_std
```
