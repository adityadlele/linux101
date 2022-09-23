Exercise 1
==========

```{admonition} Question 1
Use the code above as an example to a) plot energy of L-J potentials for He, Ne and Kr. Comment on the comparison. b) If the force on the particles
 is expressed as $F_r=\frac{dV(r)}{dr}$, plot the forces along with potential energy for the above.
```

Uncomment the line with '##' and fill up the space with your code whereever required.

```
# Boltzmann's constant, J/K
kB = 1.381e-23

# The Lennard-Jones parameters:
A = 1.024e-23   # J.nm^6
B = 1.582e-26   # J.nm^12

# Adjust the units of A and B - they have more manageable values
# in K.nm^6 and K.nm^12
A, B = A / kB, B / kB

# Interatomic distance, in nm
r = np.linspace(0.3, 1, 1000)
# Interatomic potential
U = B/r**12 - A/r**6
# Interatomic force
##F = put your code here

line1 = plt.plot(r, U, 'k', lw=2, label=r'U(r)')
plt.xlim(0.3, 0.8)
plt.ylim(-150, 100)

##plt.twinx()
##line2 = plt.plot(r, F, 'k', ls=':', lw=2, label=r'F(r)')
##plt.xlim(0.3, 0.8)
##plt.ylim(-1000, 1000)

##lines = line1 + line2
##labels = []
##for line in lines:
##    labels.append(line.get_label())
##plt.legend(lines, labels)

plt.show()
```

```{admonition} Question 2
What would happen if we do not include the repulsion part of the LJ potential?
```

```{admonition} Question 3
To test the ergodic hypothesis, use LAMMPS to simulate a system of He atoms
as described earlier with MD simulation time of 200 ps. Use conditions 298 K and 5 bar. Now use this simulation 
to determine the density of the system. Use data spanning 1, 2, 5, 10, 20 and 100 ps. You can use the data after 
initial property fluctuations are over. You can use a combination of head and tail command to randomly extract
the data. Compare the calculated density with NIST database and comment on the comparison.
```
Hints:
```sh
#Use the following line to change in.lammps from class example
pair_style      lj/cut 3.5
pair_coeff      1 1 0.02166 2.64 3.5
```

```sh
#Change geometry parameters in ar.data file in the masses section
1 4.0026  # He
```


```{admonition} Question 4

Atom No. Atom type x y z


1 O 0.0 0.0 0.0


2 H 1.1 0.0 0.0


3 H 0.2 0.9 0.0

Calculate the strain energy for this molecule, using the harmonic force field parameters below. Ignore the LJ-interactions between atoms sharing a bond. Atoms with an interatomic distance 40$\%$ longer than their equilibrium distance should be considered not bonded.

+++
$R_{OH}$ =1.0 $Ang$, $k _{OH}$ =1000 kJ/(mol*$Ang^2$ )
+++

+++
$R_{HH}$ =0.7 $Ang$, $k_{HH}$ =1000 kJ/(mol*$Ang^2$)
+++


$\theta_{HOH}$ =105$^o$, $k_{HOH}$ =0.25 kJ/(mol*degree$^2$ )

$\theta_{HHO}$ =180$^o$, $k_{HHO}$ =0.25 kJ/(mol*degree$^2$ )

$\epsilon(H)$=0.25 kJ/mol, $\sigma$(H)=2.0 $Ang$

$\epsilon(O)$=0.50 kJ/mol, $\sigma$(H)=3.8 $Ang$

The strain energy can be calculated using
+++
$$
E=\sum_{a=1}^{n-1}\sum_{b=a+1}^{n}4\epsilon\left(\left(\frac{\sigma}{r_{ab}}\right)^{12}-\left(\frac{\sigma}{r_{ab}}\right)^6\right)+\sum_{i=1}^{n_{bound}}k_b(r_i-r_o)^2+\sum_{j=1}^{n_{angles}}k_v(\theta_i-\theta_o)^2
$$
+++
```
