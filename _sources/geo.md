# Geometry Generation

## PACKMOL

[PACKMOL](http://leandro.iqm.unicamp.br/m3g/packmol/download.shtml) is a tool to generate intial MD configurations for molecules. Please download it
using the link and upload it to your home folder (/home/Princeton_ID/). The download file name should be "packmol.tar.gz". Unzip this file using the following command

```sh
tar -xf packmol.tar.gz
``` 
Change the directory to Packmol and compile it using the command

```sh
make
```


Also download the examples.zip file from the website and upload it to your home folder. Unzip it using the following command

```sh
unzip examples.zip
``` 

Change the directory to examples folder and execute following command to generate an example geometry.

```sh
/location_to_packmol/packmol < mixture.inp
```


## ASE for crystal structures

Open a juputer notebook using class allocation or your own installation. 

Copy the following content to call the required python packages to create and visualize your geometries 

```sh
from ase import Atoms, units
from ase.visualize import view
from ase_notebook import AseView, ViewConfig, get_example_atoms
from ase.io import read, write, lammpsrun
from ase import units 
import numpy as np
import matplotlib.pyplot as plt
from ase.visualize import view
from ase.build import molecule
from ase.build import bulk
from ase.spacegroup import crystal
from ase.build import surface
from ase.build import fcc111, bcc110, hcp0001, fcc100, add_adsorbate
``` 

Now let us call and view a predefined structure

```sh
#Call a predefined structure
a1 = bulk('Cu', 'fcc', a=3.6)
```

Then repeat this structure and view

```sh
##Repeat the bulk structure
a1 = a1.repeat((4,4,4))
view(a1, viewer='x3d')
```
Keep in mind that this is a bulk structure. You can write this structure into a lammps output file using following command

```sh
###Write the ouput
write('Cu.lammps-data',a1)
```

Now let us generate your own structure

```sh
## Generate your own crystal
gAl2O3 = crystal(['Al','Al','Al',
                 'Al','Al','Al',
                 'O','O','O','O',
                 'O','O','O','O'],
                 basis=[(0.377,3./4.,0.126),(0.868,1./4.,0.498),(0.875,3./4.,0.125),(0.615,3./4.,0.745),(0.367,0.075,0.612),(0.116,0.579,0.862),(0.881,3./4.,0.874),(0.614,1./4.,0.640),(0.364,3./4.,0.889),(0.132,1./4.,0.627),(0.889,0.406,0.899),(0.605,0.917,0.614),(0.357,0.406,0.853),(0.138,0.916,0.637)],
                 spacegroup=11,
                 cellpar=[5.577, 8.401, 8.069, 90, 90.59, 90])

view(gAl2O3, viewer='x3d')
```

To create different surfaces using the surface commands

```sh
##Create a surface 
a1 = bulk('Cu', 'fcc', a=3.6)
a1 = a1.repeat((4,4,4))
a1_100 = surface(a1, (1, 0, 0), 1)
view(a1_100, viewer='x3d')
```

Similarly, for gamma-alumina
```sh
## Generate your own crystal
gAl2O3 = crystal(['Al','Al','Al',
                 'Al','Al','Al',
                 'O','O','O','O',
                 'O','O','O','O'],
                 basis=[(0.377,3./4.,0.126),(0.868,1./4.,0.498),(0.875,3./4.,0.125),(0.615,3./4.,0.745),(0.367,0.075,0.612),(0.116,0.579,0.862),(0.881,3./4.,0.874),(0.614,1./4.,0.640),(0.364,3./4.,0.889),(0.132,1./4.,0.627),(0.889,0.406,0.899),(0.605,0.917,0.614),(0.357,0.406,0.853),(0.138,0.916,0.637)],
                 spacegroup=11,
                 cellpar=[5.577, 8.401, 8.069, 90, 90.59, 90])

view(gAl2O3, viewer='x3d')
gAl2O3_110 = surface(gAl2O3, (1, 0, 0), 2)

#Expand the surface
gAl2O3_110 = gAl2O3_110.repeat((4,2,1))
view(gAl2O3_110, viewer='x3d')
```

You can also manipulate your atoms, for example following cell is getting rid of atoms below a certain length

```sh
## Generate your own crystal
gAl2O3 = crystal(['Al','Al','Al',
                 'Al','Al','Al',
                 'O','O','O','O',
                 'O','O','O','O'],
                 basis=[(0.377,3./4.,0.126),(0.868,1./4.,0.498),(0.875,3./4.,0.125),(0.615,3./4.,0.745),(0.367,0.075,0.612),(0.116,0.579,0.862),(0.881,3./4.,0.874),(0.614,1./4.,0.640),(0.364,3./4.,0.889),(0.132,1./4.,0.627),(0.889,0.406,0.899),(0.605,0.917,0.614),(0.357,0.406,0.853),(0.138,0.916,0.637)],
                 spacegroup=11,
                 cellpar=[5.577, 8.401, 8.069, 90, 90.59, 90])

view(gAl2O3, viewer='x3d')
gAl2O3_110 = surface(gAl2O3, (1, 0, 0), 2)


#Expand the surface
gAl2O3_110 = gAl2O3_110.repeat((4,2,1))
del gAl2O3_110[gAl2O3_110.positions[:, 2] > 8.9]


view(gAl2O3_110, viewer='x3d')
```

You can also read an external file

```sh
##Read an external file
cement=read('/home/al9001/CaCO3.poscar')
view(cement, viewer='x3d')
```
and manipulate it

```sh
cement = cement.repeat((4,2,1))
view(cement, viewer='x3d')
```

To add adsorbates to the structure

```sh
slab = fcc100('Pt', size=(3,2,2))
add_adsorbate(slab, 'N', 1.5, 'ontop')
view(slab, viewer='x3d')
```

Now, to create a carbon nanotube

```sh
cnt = nanotube(6, 0, length=4)
view(cnt, viewer='x3d')
```

You can also attach two structures. For example to stack two nanotubes together
```sh
at2 = attach.attach(cnt,cnt,2)
view(at2, viewer='x3d')
```
