---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.10.3
kernelspec:
  display_name: ase [~/.conda/envs/ase/]
  language: python
  name: python3
---

+++ {"tags": []}

Molecular dynamics basics
=========================

Static DFT or other ab-initio calculations provide us with a lot of useful and very accurate information. However, atoms and molecules around us are in constant motion at finite temperatures.
 Molecular dynamics(MD) simulation techniques model this motion. The driving force engine behind the motion could be ab-intio calculations or empirical potentials. Several
 flavors of MD have been developed among ab-initio and classical techniques to tackle different type of processes. Empirical potentials can be further classified into reactive and 
non-reactive potentials. Recently, an additional flavor of machine learning (ML) based potentials is also being developed.  Different molecular dynamics techniques have different 
levels of accuracy and uses. 

> There are no wrong MD techniques, just wrong applications.(Prof. Adri van Duin)

## A simple example

Let us take a look at an example of a LJ potential to model the motion of an inert gas.





> This example has a historical significance. Aneesur Rahman, considered as one of the pioneers of molecular dynamics, used similar 
methods modelling motion of atoms in liquid Argon. This paper is generally considered to mark the beginnings of the molecular dynamics
 method. You can access the original paper [here](https://journals.aps.org/pr/abstract/10.1103/PhysRev.136.A405).

```{code-cell} ipython3
from ase import Atoms, units
from ase.visualize import view
from ase.calculators.lj import LennardJones
from ase.md.velocitydistribution import MaxwellBoltzmannDistribution
from ase.md.verlet import VelocityVerlet
from ase.md.langevin import Langevin
from ase.io.trajectory import Trajectory
from ase.optimize.minimahopping import MinimaHopping
from ase_notebook import AseView, ViewConfig, get_example_atoms
from ase.io import read, write, lammpsrun
from ase import units
```

Let us first define the simulation box geometry.

```{code-cell} ipython3
atoms=read('/home/al9001/Ar.xyz')
view(atoms, viewer='ngl')
```

Run a sample MD simulation.

```{code-cell} ipython3
atoms.calc = LennardJones(sigma=0.34,epsilon=1.657E-21,rc=3.4)
MaxwellBoltzmannDistribution(atoms, temp=0.00814)
T = 94.4  # Kelvin

dyn = Langevin(atoms, 0.1*units.fs, T*units.kB, 0.002)

def printenergy(a=atoms):  # store a reference to atoms in the definition.
    """Function to print the potential, kinetic and total energy."""
    epot = a.get_potential_energy() / len(a)
    ekin = a.get_kinetic_energy() / len(a)
    print('Energy per atom: Epot = %.3feV  Ekin = %.2feV (T=%3.0fK)  '
          'Etot = %.3feV' % (epot, ekin, ekin / (1.5 * units.kB), epot + ekin))


dyn.attach(printenergy, interval=50)

# We also want to save the positions of all atoms after every 100th time step.
traj = Trajectory('moldyn3.traj', 'w', atoms)
dyn.attach(traj.write, interval=50)

# Now run the dynamics
printenergy()
dyn.run(200)
```

