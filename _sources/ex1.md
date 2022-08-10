Exercise 1
==========

```{admonition} Exercise 1
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
