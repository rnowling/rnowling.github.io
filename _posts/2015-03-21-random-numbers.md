---
layout: post
title:  "Random Number Generation Horror Stories"
date:   2015-03-21 17:30:19
categories: "math"
---
Random number generation is an important component of simulation and sampling problems.  

Most applications of random number generation rely on the assumption that the random numbers are independent and identically distributed (i.i.d.).  Violation of the independence assumption can cause the random numbers to have a correlated or periodic pattern, which can negatively affect the sampling or simulation of a system. This can happen if PRNGs (psueo-random number generators) have periods much shorter than the number of numbers sampled or if software that supports checkpointing fails to re-initialize the state of the PRNG correctly.  [Cerutti, et al.](http://pubs.acs.org.proxy.library.nd.edu/doi/abs/10.1021/ct8002173) demonstrated practical situations where failures to manage the PRNG state correctly lead to detectable artifacts in molecular simulations.

Seeding PRNGs is also important.  In my recent work evaluating a population genetics package, I ran multiple simulations on a cluster to measure the quality of the sampling.  Since the package was seeding the runs based on the time, several of my runs were identical since they started at the same time on the cluster.  I ended up modifying the software to allow the user to specify a seed.  

Small, specialized software packages are not the only victims.  With the rise of data science, a wider audience will have to deal with issues related to generating random numbers. For the 1.0.x and 1.1.x releases, [Apache Spark's](https://spark.apache.org/) PySpark component had an [issue](https://issues.apache.org/jira/browse/SPARK-4148) with seeding, resulting in the same sequence being generated for multiple partitions.

I would love to see guidelines or recommendations for handling random number generation, especially in distributed systems.
