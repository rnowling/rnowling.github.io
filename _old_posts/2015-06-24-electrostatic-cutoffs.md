---
layout: post
title:  "Electrostatic Cutoffs in Molecular Dynamics"
date:   2015-06-24 12:13:19
categories: "math"
tags: ["math", "molecular dynamics", "science"]
---
Classical potentials commonly used in molecular dynamics contain [Lennard-Jones](https://en.wikipedia.org/wiki/Lennard-Jones_potential) and [Coulombic](https://en.wikipedia.org/wiki/Coulomb's_law) terms:

$$
U(x) = \sum_{i \neq j} 4 \epsilon_{ij} \Big[  \Big( \frac{\sigma_{ij}}{r_{ij}} \Big)^{12} + \Big( \frac{\sigma_{ij}}{r_{ij}} \Big)^{6} \Big] + \frac{1}{4 \pi \epsilon_0} \sum_{i \neq j} \frac{q_i q_j}{r_{ij}}
$$

In the rawest form, the electrostatic potential required $$O(N^2)$$ calculations per evaluation. [Particle Mesh Ewald](https://en.wikipedia.org/wiki/Ewald_summation#Particle_mesh_Ewald_.28PME.29_method) can we be used to reduce the complexity to $$O(N \log N)$$ but is complicated to implement.

Cutoffs are a simpler approach.  At distances of about 1 nm and greater, [the electrostatic forces are small enough to ignore without significant effects on accuracy](http://www.ncbi.nlm.nih.gov/pubmed/15641786).  With an abrupt truncation, the electrostatics are simply ignored beyond a cutoff radius $$r_c$$:

$$
U(x) = \left\{
\begin{array}{lr}
4 \epsilon_{ij} \Big[ \Big( \frac{\sigma_{ij}}{r_{ij}} \Big)^{12} + \Big( \frac{\sigma_{ij}}{r_{ij}} \Big)^{6} \Big] + \frac{1}{4 \pi \epsilon_0} \frac{q_i q_j}{r_{ij}} & \text{if } r_{ij} \leq r_c \\
0 & \text{if } r_{ij} > r_c
\end{array}
\right.
$$

However, the resulting potential isn't continuous, resulting in a failure to conserve energy.  A [switching function](http://onlinelibrary.wiley.com/doi/10.1002/prot.340060104/abstract) $$S$$ can be used to smoothly switch the potential to zero in a boundary region ($$[r_s, r_c]$$) to produce a continuous function:

$$
U(x) = \left\{
\begin{array}{lr}
4 \epsilon_{ij} \Big[ \Big( \frac{\sigma_{ij}}{r_{ij}} \Big)^{12} + \Big( \frac{\sigma_{ij}}{r_{ij}} \Big)^{6} \Big] + \frac{1}{4 \pi \epsilon_0} \frac{q_i q_j}{r_{ij}} & \text{if } r_{ij} < r_s \\
S_{r_s, r_c}(r_{ij}) \, 4 \epsilon_{ij} \Big[ \Big( \frac{\sigma_{ij}}{r_{ij}} \Big)^{12} + \Big( \frac{\sigma_{ij}}{r_{ij}} \Big)^{6} \Big] + S_{r_s, r_c}(r_{ij}) \, \frac{1}{4 \pi \epsilon_0} \frac{q_i q_j}{r_{ij}} & \text{if } r_s \leq r_{ij} \leq r_c \\
0 & \text{if } r_{ij} > r_c
\end{array}
\right.
$$

Cutoffs with a switching function also happen to be the fastest method, requiring only $$O(N)$$ calculations per evaluation.

Performance improvements are not the only reason to use switching and cutoffs, however.  When I first tried to simulating water boxes, I failed to use any form of cutoffs.  As a result, the waters formed a rigid lattice with no diffusion activity.  (This was less of an issue with solvated proteins since the protein disrupted the periodicity of the potential.)  After several weeks of frustrated effort, I noticed that most popular MD software packages employ cutoffs by default.  Once I changed my forcefield settings to switch the electrostatics, I was able to recover proper hydrogen bonding networks and diffusion rates.

