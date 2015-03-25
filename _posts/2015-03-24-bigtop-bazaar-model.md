---
layout: post
title:  "BigTop Bazaar: Simulating a Bazaar"
date:   2015-03-24 12:13:19
categories: "math"
---

Where do you get data for building and testing a real-time analytics system for a conference?  Simulate it!

Assume that customers are given badges with RFID tags so that their movements can be tracked, recorded, and analyzed in real time.  We want to simulate their movement through a conference floor as they visit company booths.  I propose a model combining physics and machine learning models.

## Model Description ##
First, we want to quantify the interactions between individuals booths and individual customers.  We can do this by reverse engineering a factor analysis model with latent factors.  A factor analysis model assumes that the relationship between customers and products can be modeled using a lower dimensional space (i.e., customers represent combinations of a few basic profiles.) A factor model can be written like so:

$$
Z=LF + \epsilon
$$

where $$Z$$ is a $$M$$x$$N$$ matrix giving the interaction strengths (preferences) for $$N$$ customers and $$M$$ products.  The $$M$$x$$K$$ matrix $$L$$ gives the $$K$$ latent factors with weights for each of $$M$$ products.  The $$K$$x$$N$$ matrix $$F$$ gives the customers’ weights for each of the latent factors.  Note that $$k << N$$.  The part of the customers’ interactions that cannot be accounted for by the latent factors is modeled as error ($$\epsilon$$) and usually assumed to be Gaussian distributed.

We model customer preferences for booths by randomly generating the matrices $$L$$ and $$F$$ and computing $$Z$$.  (The error $$\epsilon$$ is optional.)  Generating the latent factors instead of directly generating $$Z$$ is preferable since it ensures that the behavior of groups of customers will be driven by the same set of patterns.

Once the preferences for each customer are known, we model the customer movement as particles in a 2D space with booths as attractive sources.  We can describe the attractive effect of the booths using an inverted, scaled Gaussian:

$$
U(x) = \sum_{i,j} -\frac{s_{i, j}}{\sqrt{2 \pi \sigma^2}} e^{-\frac{|x_i - x_j|^2}{2 \sigma^2}} + B
$$

where $$x_i$$ is a 2D vector of the position of the customer $$i$$, $$x_j$$ is a 2D vector of the position of the booth $$j$$, and $$s_{i,j}$$ is the interaction strength between customer $$i$$ and booth $$j$$.  The $$s_{i, j}$$ values come from the matrix $$Z$$.  (Note that the interactions may need to be scaled to get the desired "residence times.") The function B represents a force acting as a boundary condition (e.g., the shape of the room) so that particles don’t float off into oblivion. (Note that there are no interactions between the particles themselves -- we could add some if desired.)

The positions of the particles in time is found by numerically integrating the Langevin Equation:

$$
m\frac{d^2 x}{dt^2} = -\frac{dU}{dx} -\gamma m\frac{dx}{dt} + \sqrt{2 k_b T} \gamma^{1/2} m^{1/2} dW(t)
$$

The [Langevin equation](http://rnowling.github.io/math/2015/03/21/langevin-equation.html) is the standard $$F=ma$$ plus a damping term and a random term.  The damping and random term are related by the variable $$\gamma$$.  The Langevin equation provides us with a few advantages including injecting randomness into the customers’ paths and allowing the velocities of the customers to vary so they can "escape" the pull of the magnets.  (The distribution of the velocities is controlled by the temperature $$T$$ -- we can choose $$T$$ so that the velocities are inline with walking speed.)

## Analysis ##
The customers’ preferences for certain booths can be inferred by measuring the residence time, or basically, the elapsed time between a customer entering and leaving the radius of a booth.

## Implementation and Evaluation ##
Infuture posts, I'll describe my implementation and evaluation of the model.

(Thanks to [Will Benton](http://web.willbenton.com/) and [Jay Vyas](http://jayunit100.blogspot.com/) for thoughtful discussion.)


