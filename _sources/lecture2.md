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

Lecture 2
=========
##Ergodic hypothesis

We discussed an MD simulation related to liquid Ar previously. In this simulations, we modeled Ar liquid at 85 K using XYZ atoms. If you consider a
vessel full of liquid Ar with a volume of 10$^{-3}$ $m^3$, it will contain 1.4096 kg of Ar or 2.125*10$^{25}$ Ar atoms. Considering that this is a fluid,
the arrangement of atoms is not periodic either. These atoms could arrange themselves in so many ways. If we chose any XYZ atoms from this vessel, 
we would find a very large number of configurations or states. The macroscopic properties that we observe are a result of average of these configurations.
How does our system of XYZ atoms would predict the macroscopic properties? 

Of course, we can model a large number of configurations and then take an average to calculate the property of interest.
However, a better solution is given by the ergodic hypothesis. It states that over long periods of time, all the states of 
the system are equiprobable. In other words, time average of a property is same as sampling multiple states. As you can imagine, this is 
really useful!

```{admonition} Exercise x
To test the ergodic hypothesis, use the MD code/LAMMPS to determine the density and/or velocity distribution of a system of XYZ Ar atoms
as described earlier with MD simulation times of x, 10x and 100x ps. Sample the properties for every 100 timesteps with a timestep of x fs.
Compare the calculated property with the experimental result of X.  
```

 
##Ensembles

Our MD simulations code described earlier conserved the number of particles (N), volume (V) and total energy (E) of the system. This is referred to 
as the NVE or the microcanonical ensemble. It is often more convinient to perform simulations at constant temperature or pressure. This is achieved through
NVT and NPT ensembles respectively.
