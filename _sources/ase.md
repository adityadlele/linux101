# ASE Tutorial


### NEB Example

Open a Jupyter notebook interface and copy following code block

```sh
from ase.build import fcc100, add_adsorbate
from ase.constraints import FixAtoms
from ase.calculators.emt import EMT
from ase.optimize import QuasiNewton
from ase.io import read
from ase.neb import NEB
from ase.optimize import BFGS
```

Then let us load an Aluminium slab with Au atom adsorbed on top. We will then energy minimize this structure

```sh
# 2x2-Al(001) surface with 3 layers and an
# Au atom adsorbed in a hollow site:
slab = fcc100('Al', size=(2, 2, 3))
add_adsorbate(slab, 'Au', 1.7, 'hollow')
slab.center(axis=2, vacuum=4.0)

# Fix second and third layers:
mask = [atom.tag > 1 for atom in slab]
# print(mask)
slab.set_constraint(FixAtoms(mask=mask))

# Use EMT potential:
slab.calc = EMT()

# Initial state:
qn = QuasiNewton(slab, trajectory='initial.traj')
qn.run(fmax=0.05)

# Final state:
slab[-1].x += slab.get_cell()[0, 0] / 2
qn = QuasiNewton(slab, trajectory='final.traj')
qn.run(fmax=0.05)
```

Run the following code block to run NEB calculations

```sh
initial = read('initial.traj')
final = read('final.traj')

constraint = FixAtoms(mask=[atom.tag > 1 for atom in initial])

images = [initial]
for i in range(3):
    image = initial.copy()
    image.calc = EMT()
    image.set_constraint(constraint)
    images.append(image)

images.append(final)

neb = NEB(images)
neb.interpolate()
qn = BFGS(neb, trajectory='neb.traj')
qn.run(fmax=0.05)
```

To visualize the geometry, open a virtual machine on Adroit visualization node. Change the directory to your home directory.
Execute the following command

```sh
/opt/export/course/mae539/anaconda3/envs/myenv/bin/ase gui neb.traj@-5:
```

### L-J parameter fitting

Use the following codeblocks in a Jupyter notebook to run parameter fitting example.

```sh
import matplotlib.pyplot as plt
import numpy as np

r = np.arange(3.5, 7., 0.5)
energy = np.array([0.1374, -0.0195, -0.0218, 
                   -0.0133, -0.0076, -0.0043, 
                   -0.0025])
energy_err = energy * 0.1

plt.errorbar(r, energy, yerr=energy_err, 
             marker='o', ls='')
plt.xlabel(r'$r$/Å')
plt.ylabel(r'$E$/eV')
plt.show()
```

Then

```sh
from scipy.optimize import curve_fit

def lj_energy(r, epsilon, sigma):
    """
    Implementation of the Lennard-Jones potential 
    to calculate the energy of the interaction.
    
    Parameters
    ----------
    r: float
        Distance between two particles (Å)
    epsilon: float 
        Potential energy at the equilibrium bond 
        length (eV)
    sigma: float 
        Distance at which the potential energy is 
        zero (Å)
    
    Returns
    -------
    float
        Energy of the van der Waals interaction (eV)
    """
    return 4 * epsilon * np.power(
        sigma / r, 12) - 4 * epsilon * np.power(
        sigma / r, 6)

popt, pcov = curve_fit(lj_energy, r, energy, 
                       sigma=energy_err)
print('Best value for ε = {:.2e} eV'.format(
    popt[0]))
print('Best value for σ = {:.2f} Å'.format(
    popt[1]))
```

And finally, to plot the fitted parameters against the intial data

```sh
plt.errorbar(r, energy, yerr=energy_err, marker='o', ls='')
x = np.linspace(3.5, 7, 1000)
plt.plot(x, lj_energy(x, popt[0], popt[1]))
plt.xlabel(r'$r$/Å')
plt.ylabel(r'$E$/eV')
plt.show()
```
