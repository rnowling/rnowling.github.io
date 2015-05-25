---
layout: post
title:  "The Lennard-Jones Potential Can Cause Simulations to Blow Up"
date:   2015-05-25 12:23:19
categories: "science"
tags: ["math", "science", "biophysics", "molecular dynamics"]
---
Most popular molecular dynamics force fields employ the [Lennard-Jones potential](http://en.wikipedia.org/wiki/Lennard-Jones_potential) to model, among other things, the volume of atoms.  The LJ potential can be a source of numerical instability, however, causing the potential energy of the system to grow exponentially towards infinity.

Numerical integration of the equations of motion is performed in steps over discrete periods of time.  The time step is limited by the fastest frequencies in the system.  Usually, the fastest motions come from bond vibrations of hydrogen atoms, limiting the time step to about 1 fs.

The Lennard-Jones appears frequently in the 6-12 form:

$$
U(x) = 4\epsilon \Big[ \Big( \frac{\sigma}{r} \Big)^{12} - \Big( \frac{\sigma}{r} \Big)^{6} \Big]
$$

As the distance $$r$$ between two atoms approaches zero, the LJ potential and its first derivative (where $$F = - 
\nabla U$$ are the forces) go to infinity.  When two atoms are too close, frequencies faster than those of the hydrogen bonds can manifest.  Without adjusting the time step to compensate for the faster frequencies, a resonance effect occurs and the system blows up due to the magnitude of the energy growing beyond what is representable with floating-point arithmetic.

Thankfully, the LJ potential rarely causes such instabilities in equilibrated systems.  In most equilibrated systems, atoms tend to repel each other before they become too close.  Close contacts tends to occur when molecular structures haven't been properly minimized or equilibrated before starting a simulation.

This isn't true in all cases, though.  When working on the [BigTop Bazaar](http://rnowling.github.io/math/2015/03/24/bigtop-bazaar-model.html), I tried a Lennard-Jones potential before settling on a Gaussian potential.  With high temperatures, the magnitude of the random noise was large enough to frequently place a particle too close to a source, causing resonance and numerical instabilities.  Since I was using the [Langevin equation](http://rnowling.github.io/math/2015/03/21/langevin-equation.html), the simulations were robust against small spikes in the potential energy but not large spikes. To compensate, I had to increase the damping coefficient, moving the dynamics into the over-damped regime and affecting the behavior of the dynamics.  In the end, I chose a Gaussian potential since a Gaussian is bounded by a finite value, avoiding the numerical instabilities.

In conclusion, modelers should be aware of the potential instability associated with the Lennard-Jones potential. Molecular dynamics users should take care to properly minimize and equilibrate their simulations to reduce the risks of numerical instabilities.  In new models, the LJ potential should be used with caution.