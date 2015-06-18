---
layout: post
title:  "The Nos&eacute;-Hoover-Langevin Thermostat"
date:   2015-06-01 10:41:19
categories: "math"
tags: ["math", "molecular dynamics", "science"]
---
Dynamics modeled using Newton's equations of motion sample from the [microcanonical ensemble](https://en.wikipedia.org/wiki/Microcanonical_ensemble).  This is what you were taught in introductory physics -- conservation of energy.  An alternative is to model systems with the [canonical ensemble](https://en.wikipedia.org/wiki/Canonical_ensemble), where the temperature is fixed but the total system energy is allowed to vary.  Fixed temperature is closer to experimental conditions and thus often preferred in molecular simulation.

The [Langevin equation](http://rnowling.github.io/math/2015/03/21/langevin-equation.html) is a popular choice as a stochastic thermostat, or method for controlling the temperature of a simulation.  The Langevin equation can also be used to emulate the effects of solvent through the damping and random noise terms, making it a doubly-attractive choice for thermostatting implicit solvent simulations.  The downside of the Langevin equation is that it can disruptive if the damping coefficient is too large.

[Leimkuhler, et al.](http://www.maths.ed.ac.uk/~mthdat25/thermostat/A-gentle-stochastic-thermostat.pdf) and [Samoletov, et al.](http://arxiv.org/pdf/physics/0412163.pdf) studied a stochastic extension to the Nos&eacute;-Hoover thermostat deemed Nos&eacute;-Hoover Langevin.  In the NHL scheme, the dynamics are described in an extended phase space $$(\mathbf{q}, \mathbf{p}, \zeta)$$ by the equations

$$
d\mathbf{q} = M^{-1} \mathbf{p}dt \\
d\mathbf{p} = -\nabla U (\mathbf{q})dt - \zeta \mathbf{p}dt \\
Qd\zeta = \Big[ \mathbf{p}^{T} M^{-1} \mathbf{p} - 3 N k_B T \Big] dt - \gamma Q \zeta dt + \sqrt{2 k_B T} \gamma^{1/2} Q^{1/2} dW(t)
$$

In the Langvin equation, the damping and random noise are applied directly to each degree in freedom.  In the NHL model, the momenta are rescaled by $$\zeta$$, which is subject to damping and random noise.  Leimkuhler, et al. demonstrate evidence that NHL converges to the target kinetic energy more quickly and perturbing dynamics less than the Langevin equation.  Further study is needed to assess the whether NHL is advantageous on large biomolecular systems.