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

Assume there is no analytical equation for $$x(t)$$ that satisfied the differential equation.  We can use a numerical integration algorithm to compute values of $$x(t)$$ on discrete time steps $$\Delta t$$.  We'll focus on explicit integrators which extrapolate the $$x(t_i)$$ for each discrete step $$i$$ from the previous value $$x(t_{i-1})$$.

The Leapfrog method is commonly-used in molecular dynamics.  Here, we'll walk through the steps of deriving the Leapfrog method.

We begin by finding the Maclaurin series of $$a(t)$$, $$x(t)$$, and $$v(t)$$:

$$
a(\Delta t) = a(0) + a'(0)\Delta t + \mathcal{O}(\Delta t)^2 \\
v(\Delta t) = v(0) + v'(0)\Delta t + \frac{1}{2}v''(0)\Delta t^2 + \mathcal{O}(\Delta t^3) \\
x(\Delta t) = x(0) + x'(0)\Delta t + \frac{1}{2}x''(0)\Delta t^2 + \mathcal{O}(\Delta t^3)
$$

We need to re-arrange and make substitutions of the terms in the Maclaurin series for $$x(\Delta t)$$ and $$v(\Delta t)$$ so that they only contain the values $$x(0)$$, $$v(0)$$, $$a(0)$$, and $$a(\Delta t)$$. We begin by truncating the Maclaurin series of $$a(\Delta t)$$ after the first-order term and re-arrange:

$$
a(\Delta t) = a(0) + a'(0)\Delta t \\
a'(0)\Delta t = a(\Delta t) - a(0)
$$

We then note the following relationships:

$$
\frac{d^2 x}{dt^2} = a(t) = -m^{-1} \nabla(x(t)) \\
v''(t) = a'(t) \\
x''(t) = v'(t) = a(t) \\
x'(t) = v(t) 
$$

By truncating the Maclaurin series for $$x(\Delta t)$$ and $$v(\Delta t)$$ at the second-order terms and applying the above substitutions, we get a variation of the Leapfrog integrator:

$$
a(0) = -m^{-1} \nabla(x(0)) \\
x(\Delta t) = x(0) + v(0)\Delta t + \frac{1}{2}a(0)\Delta t^2 \\
a(\Delta t) = -m^{-1} \nabla(x(\Delta t)) \\
v(\Delta t) = v(0) + \frac{1}{2} (a(0) + a(\Delta t)) \Delta t \\
$$

Since we truncated the equations for $$x(\Delta t)$$ and $$v(\Delta t)$$ at the second-order terms, the Leapfrog integrator is second-order accurate for a single update of the velocities and positions.  This means that if we half the time step, the per-step error is divided by eight.

In a future blog post, we'll compare the analytical model for the harmonic oscillator to approximations made with the Leapfrog integrator.  This comparison will allow us to verify the order of the error of the Leapfrog integrator.
