# Simulation Tools

## Python

We will be using Jupyter notebooks for first few classes. Jupyter notebook allows us to interactively use Python. First you should use following command in your *home* directory. 
```sh
module load anaconda3/2021.11
conda create --name myenv <package-1> <package-2> ... <package-N>
```
If you are already familiar with Python or interested in further exploring it use the following [link](https://researchcomputing.princeton.edu/support/knowledge-base/python) for instructions on using Python on Princeton clusters 

We will use Jupyter notebook to access the above conda environment. Please follow the research computing [instructions](https://researchcomputing.princeton.edu/support/knowledge-base/jupyter#ondemand) to run Jupyter on your web-browser. Make a python notebook and name it *test* with the following code 

   

```sh
Python test
```


## Atomistic simulation environment (ASE)

[ASE](https://wiki.fysik.dtu.dk/ase/index.html) is a Python based simulation environment that can be used to manage/create/control your simulations on 
multiple standard simulation softwares. We are going to cover some use cases of ASE. We will use it through the interactive Python environment.  

```{admonition} Create an ASE envoronment on Adroit using conda
Execute the following commands
1. Load the anaconda module 

`module load anaconda3/2021.11`

2.Create a conda environment ASE

`conda create --name ase ase pandas matplotlib --channel conda-forge`

3.To check the environment execute the following

`conda activate ase`

You should see a small **(ase)** text at the start of your command line

```

## LAMMPS

### Typical input files required (The names can be changed)

- in.lammps: Input file. This file has all the parameters controlling the simulations (e.g. Thermostat, Barostate, Ensemble) 
- geom.xyz: (optional) Geometry file. This file describes the coordinates of your simulation system. Alternatively, you can also define your system geometry in the input file.
- potential file: (optional) A file defining the MD potential that you are using.

### LAMMPS example script

We will try to use a LAMMPS executable that has been already compiled

Use the following job submission script to submit a LAMMPS job
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

srun $HOME/.local/bin/lmp_adroit -in in.melt
```

The input file can be copied from below

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

thermo          5000
run             10000
```

## VASP

### Reiterating some basics 

Files used to run VASP calculations
- INCAR: This file describes the input parameters required for different calculations (For example: A molecule will have different parameters than a crystal)
- KPOINTS: Description of receprocal space
- POSCAR: Define the geometry of your system
- POTCAR: Concatenated pseudopotentials for elements of your system 

### Running VASP on Adroit

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
