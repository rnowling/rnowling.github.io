---
layout: post
title:  "Deriving the Leapfrog Integrator"
date:   2016-11-11 12:13:19
categories: "math"
tags: ["math"]
---
Assume we have a second-order ordinary differential equation

$$
m\frac{d^2 x}{dt^2} = -\nabla U(x)
$$

with initial conditions $$x(0)$$ and $$v(0)$$.

Assume there is no analytical equation for $$x(t)$$ that satisfied the differential equation.  We can use a numerical integration algorithm to compute values of $$x(t)$$ on discrete time steps $$\Delta t$$.  We'll focus on explicit integrators which extrapolate $$x(t + \Delta t)$$ from the previous value $$x(t)$$.

The Leapfrog method is commonly-used in molecular dynamics.  Here, we'll walk through the steps of deriving the Leapfrog method.

We begin by finding the Taylor series of $$a(t + \Delta t)$$, $$x(t + \Delta t)$$, and $$v(t + \Delta t)$$ around $$t$$:

$$
a(t + \Delta t) = a(t) + a'(t)\Delta t + \mathcal{O}(\Delta t)^2 \\
v(t + \Delta t) = v(t) + v'(t)\Delta t + \frac{1}{2}v''(t)\Delta t^2 + \mathcal{O}(\Delta t^3) \\
x(t + \Delta t) = x(t) + x'(t)\Delta t + \frac{1}{2}x''(t)\Delta t^2 + \mathcal{O}(\Delta t^3)
$$

We need to re-arrange and make substitutions of the terms in the Taylor series for $$x(t + \Delta t)$$ and $$v(t + \Delta t)$$ so that they only contain the values $$x(t)$$, $$v(t)$$, $$a(t)$$, and $$a(t + \Delta t)$$. We begin by truncating the Taylor series of $$a(t + \Delta t)$$ after the first-order term and re-arrange:

$$
a(t + \Delta t) = a(t) + a'(t)\Delta t \\
a'(t)\Delta t = a(t + \Delta t) - a(t)
$$

We then note the following relationships:

$$
\frac{d^2 x}{dt^2} = a(t) = -m^{-1} \nabla U(x(t)) \\
v''(t) = a'(t) \\
x''(t) = v'(t) = a(t) \\
x'(t) = v(t) 
$$

By truncating the Taylor series for $$x(t + \Delta t)$$ and $$v(t + \Delta t)$$ at the second-order terms and applying the above substitutions, we get a variation of the Leapfrog integrator:

$$
a(t) = -m^{-1} \nabla U(x(t)) \\
x(t + \Delta t) = x(t) + v(t)\Delta t + \frac{1}{2}a(t)\Delta t^2 \\
a(t + \Delta t) = -m^{-1} \nabla U(x(t + \Delta t)) \\
v(t + \Delta t) = v(t) + \frac{1}{2} [a(t) + a(t + \Delta t)] \Delta t \\
$$

Since we truncated the equations for $$x(t + \Delta t)$$ and $$v(t + \Delta t)$$ at the second-order terms, the Leapfrog integrator is second-order accurate for a single update of the velocities and positions.  This means that if we half the time step, the per-step error is divided by eight.

In a future blog post, we'll compare the analytical model for the harmonic oscillator to approximations made with the Leapfrog integrator.  This comparison will allow us to verify the order of the error of the Leapfrog integrator.
