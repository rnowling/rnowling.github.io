---
layout: post
title:  "Langevin Equation"
date:   2015-03-21 16:55:19
categories: "math"
---

A significant portion of my Ph.D. work was focused on the use of the [Langevin equation](http://en.wikipedia.org/wiki/Langevin_dynamics) for simulating molecular movements.  The Langevin equation can be seen as an "extension" of standard Newtonian mechanics with the addition of damping and random noise terms.  The Langevin equation is given as follows:

$$
m\ddot{x} = -\nabla U(x) - \gamma m \dot{x} + \sqrt{2 k_B T} \gamma^{1/2} m^{1/2} dW(t)
$$

Of course, the question, "Why do we use the Langevin equation?" comes to mind.  There are three reasons.  The most common use case are implicit-solvent simulations.  The dynamics of biomolecules are strongly dependent on interactions with solvents such as water, however simulating with water molecules greatly increases (usually by 10x or more) the number of atoms that need to be simulated.  Since molecular dynamics simulations are very computationally intensive, removing the water molecules and changing the model to approximate the effects of the solvent significantly decreases run time. Implicit-solvent models usually combine Langevin dynamics with an additional potential.  The Langevin equation approximates two effects of solvent: damping due to friction as the biomolecules "rubs" against the solvent and random noise that simulates the effect of the solvent molecules "bumping" into the biomolecule.  Potentials such as SCP-ISM and the Generalized Born models approximate the electrostatic effects of solvent. 

This sort of coarse graining is not unique to molecular dynamics -- many areas of physics employ models with separations of timescales.  As a result, the Langevin equation is broadly used to approximate the effect of the fast timescales with random noise.

For both implicit- and explicit-solvent simulations, Langevin dynamics enables simulation in the canonical ensemble (i.e, fixed temperature, varying energy).  By fixing the temperature, simulations are more comparable to laboratory conditions where the temperatures are fixed.  However, more realistically, the main advantage of fixed temperature simulations is that Langevin dynamics preserve ergodicity.  To state it another way, Langevin dynamics allows the energy to vary, enabling systems to overcome potential energy barriers and improving sampling.

The third, and less well known, feature of Langevin dynamics is improving the numerical stability of simulations.  Simulations become unstable when forces change too quickly relative to the timestep.  In common molecular dynamics models, the Lennard-Jones potential, which is used to give atoms volume, contains a short repulsive term that tends towards infinity as the distance goes to zero.  In equilibrium, most atoms will be repulsed before getting too close for the potential to cause a problem.  However, two atoms can come in close contact if the system is not properly minimized, the timestep is too large, or the temperature is too high.  Due to the damping term, the Langevin equation can aid in preventing the effect of the potential driving the system into an unstable state.  The Langevin equation can also be used for equilibration by simulating the system with a large damping value (e.g., 10,000/ps) and a low temperature (e.g., 10K) and slowly decreasing the damping value and increasing temperature.

In my Ph.D. work, the Langevin equation became a powerful tool in my toolbox.  Much of my time was spent adapting the Langevin equation to new uses cases -- I found that it was incredibly versatile and always leading to new insights.

