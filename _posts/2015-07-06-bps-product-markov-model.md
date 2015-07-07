---
layout: post
title:  "BigPetStore Product Purchasing Markov Model"
date:   2015-07-06 12:13:19
categories: "math"
tags: ["math", "bigpetstore"]
---
Over the last year and a half, I developed a [new model](https://github.com/rnowling/bigpetstore-data-generator/raw/master/bdcloud_paper/latex/paper.pdf) for synthetic data generation in BigPetStore (BPS).  The goal of the new model was to express a wider variety of complex patterns, enabling the BPS data to be used in realistic big data processing examples.   I used Markov Models to model the customer's purchasing patterns within a given product category such as dog food.  I've been rethinking how I parameterized the model and am putting down some notes here.

My goals for the dynamics are as follows:

* Customers have clear preferences for certain field values (such as "potatoes" or "soy") over other values ("corn")
* Customers tend to buy the same product repeatedly, rotate between a few preferred products, or just pick products randomly
* Patterns should be clearly distinguishable (e.g., customers can be segmented relatively easily)

Experimental Setup
==================
Markov models with a finite number of discrete states and operating on discrete timesteps can be presented using a stochastic transition matrix giving the transition probabilities between pairs of states.  In most cases, folks are interested in the long-time, or equilibrium, behavior of a markov model -- the equilibrium distribution of the states.  The equilibrium distribution is given by the largest eigenpair for the stochastic transition matrix.

In our case, we are interested in the short-time behavior.  Customers may only make 6-12 purchases of a particular product category (e.g., dog food) per year.  As such, the customers' trajectory will be heavily influenced by the initial state and won't have sufficient time to equilibrate (decorrelate).  To recreate this, I ran 100 simulations, each of 10 transitions, from each starting state and computed the resulting histogram of population counts for the states.  I used 9 products with 2 fields, each with 3 possible values.

Current Model
=============
The current Markov Models are completely connected -- it is possible to transition from any product to any other product.  The weights between each pair of products are determined based on the equality of the field values and the importance of each field.  Self-loops are parameterized separately using the self-loop probability $$p_l$$:

$$
Pr(X_{n+1}=x|X_n=y) = 
 \left\{ 
  \begin{array}{l l}
   (1.0 - p_l) \, Pr(x,y)  & \quad \text{if $x \neq y$}\\
   p_l & \quad \text{if $x = y$}
  \end{array} \right.
$$

If the transition is between two separate products $$x$$ and $$y$$, the probability $$Pr(x, y)$$ is computed as a sum of field importance weights $$w_f$$ and field value equalities $$w_{f,s}$$:

$$
Pr(x,y) = \frac{\sum_f w_f w_{f,s}(x, y)}{\sum_{j \neq x} \sum_f w_f w_{f,s}(x, j)}
$$

$$
w_{f,s}(x,y) = \left\{ 
  \begin{array}{l l}
   w_{f,s}  & \quad \text{if $x$.$f$ = $y$.$f$}\\
   1 - w_{f, s} & \quad \text{if $x$.$f$ $\neq$ $y$.$f$}
  \end{array} \right.
$$

The parameters $$p_l$$, $$w_f$$, and $$w_{f, s}$$ (for each field $$f$$) are sampled from bimodal, bounded Gaussian distributions.  The choice of bimodal Gaussian distributions is motivated by a desire to provide weights that nearly discrete.

To explore the dynamics of the current model, I parameterized the model as follows: $$p_l = 0.8$$, $$w_1 = 1.0$$, $$w_2 = 1.0$$, $$w_{1, s} = 0.2$$, and $$w_{2, s} = 0.8$$.  I got the following population counts:

    ('chicken', 'soy'): 964
    ('beef', 'potatoes'): 1023
    ('pork', 'potatoes'): 1054
    ('beef', 'corn'): 958
    ('chicken', 'potatoes'): 1068
    ('pork', 'corn'): 971
    ('beef', 'soy'): 997
    ('chicken', 'corn'): 982
    ('pork', 'soy'): 983

Note that the population counts are nearly uniform!  Not good!  The reason for this lies in the self-transitions:

    ('chicken', 'soy'): 340
    ('beef', 'potatoes'): 320
    ('pork', 'potatoes'): 332
    ('beef', 'corn'): 304
    ('chicken', 'potatoes'): 324
    ('pork', 'corn'): 375
    ('beef', 'soy'): 344
    ('chicken', 'corn'): 306
    ('pork', 'soy'): 284

Since we use the same loopback weight regardless of the preference of the current and proposed states, we end up getting stuck in single state over the 10 states of the customer's trajectory.


Conclusion
==========
The model has a few weaknesses:

* We can only express whether it's important for a particular field to be equal or not.  We cannot express preferences for specific values such as "corn" or "wheat".
* Even if a customer prefers to choose the next product with inequal field values, the loopback weight will dominate, causing the customer to pick the same product repeatedly.
* The resulting population counts are nearly uniformally distributed!
* The model requires $$O(N_p^2 N_f)$$ calculations due to normalization requirements.

As a result, we need to fix the model.  As I explore additional model ideas, I'll write them up here.
