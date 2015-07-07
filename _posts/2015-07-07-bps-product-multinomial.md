---
layout: post
title:  "BigPetStore Product Purchasing Multinomial Model"
date:   2015-07-07 12:13:19
categories: "math"
tags: ["math", "bigpetstore"]
---
In my [last post](http://rnowling.github.io/math/2015/07/06/bps-product-markov-model.html), I described the current Markov Models used in the BigPetStore data generator.  I described a couple weaknesses of the model, including the ability to only describe transitions in terms of the (in)equality of fields and a tendency to produce a uniform distribution.  As part of my efforts to design a new model, I'm analyzing a multinomial model where each product is chosen independently of the other products (e.g., not Markovian).

As a reminder, my goals for the dynamics are as follows:

* Customers have clear preferences for certain field values (such as "potatoes" or "soy") over other values ("corn")
* Customers tend to buy the same product repeatedly, rotate between a few preferred products, or just pick products randomly
* Patterns should be clearly distinguishable (e.g., customers can be segmented relatively easily)

I'll use the same experimental setup as last time.

Multinomial Model
=================
In the multinomial model, for each customer, weights will be sampled for each field value.  For example, we may use weights of 0.2, 0.2, and 0.8 for the values "corn", "soy", and "potatoes" for the field "grains".  As in the previous model, I would generate weights from a mixture of Gaussian distributions.  The weight of each product $$p$$ is then computed as a product of the weights of the product's field values $$w_{f, v} \geq 0$$:

$$
w(p) = \prod_f w_{f, p.f}
$$

The frequencies $$F(p)$$ of each product are given by normalizing the weights:

$$
F(p) = \frac{w(p)}{\sum_j w(j)}
$$

Products can be sampled from $$F$$ using a Roulette Wheel or the [Metropolis-Hastings algorithm](https://en.wikipedia.org/wiki/Metropolis%E2%80%93Hastings_algorithm).

I ran simulations using the multinomial model.  As in my previous post, I had 9 products composed from 2 fields with 3 values each.  I used field value weights of Chicken - 0.2, Pork - 0.8, Beef - 0.8, Corn - 0.2, Soy - 0.2, and Potatoes - 0.8.  I got the following population counts

    ('chicken', 'soy'): 171
    ('beef', 'potatoes'): 2627
    ('pork', 'potatoes'): 2638
    ('beef', 'corn'): 709
    ('chicken', 'potatoes'): 671
    ('pork', 'corn'): 675
    ('beef', 'soy'): 697
    ('chicken', 'corn'): 182
    ('pork', 'soy'): 630

and the following self-transitions:

    ('chicken', 'soy'): 1
    ('beef', 'potatoes'): 772
    ('pork', 'potatoes'): 758
    ('beef', 'corn'): 56
    ('chicken', 'potatoes'): 35
    ('pork', 'corn'): 43
    ('beef', 'soy'): 53
    ('chicken', 'corn'): 4
    ('pork', 'soy'): 43

Compared with the current Markov Model, the multinomial model gives us a nice separation in the population counts between highly-weighted products such as `('beef', 'potatoes')`, medium-weight products such as `('beef', 'corn')` and low-weight products such as `('chicken', 'corn')`.  Although the products are sampled independently, we get the appearance of correlation due to the separation in the probabilities.


Conclusion
==========
The multinomial model improves over the current Markov model on several fronts:

* We can directly express preferences for specific field values
* We get a clear separation between preferrences
* Lower computational cost of $$O(N_p N_f)$$

Further work will be needed to analyze the resulting dynamics.  What happens if we have a large number of products in a large field space?  Can the customers' purchasing profiles be easily and clearly segmented?

I'll also continue to try additional Markov model ideas.
