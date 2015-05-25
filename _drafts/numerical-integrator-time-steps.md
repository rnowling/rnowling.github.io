---
layout: post
title:  "Why Spark?"
date:   2015-04-07 9:38:19
categories: spark
tags: "spark"
---
We're big fans of Spark's programming model, freely switching between functional programming with RDDs and SQL queries.  The model is more expressive and convenient programming than competitors such as MapReduce.  Combined with libraries for machine learning and graph analysis, Spark represents an "all-in-one" solution.  It's simpler and easier to use Spark for everything rather than picking and choosing between the numerous and fractured components of the Hadoop ecosystem.

Spark's elasticity and ability to cache intermediate data in memory make it better suited to cloud computing. Cloud computing offers an attractive deployment target.  Users can scale computing resources up and down as needed and avoid the complexities of managing physical servers.  Due to the provenance built into RDDs, Spark can handle the failure or removal of workers by regenerating lost partitions as needed and scale up as new workers are added -- a perfect fit for the flexibility of the cloud.  

However, the abstractions providing this flexibility come with a cost -- the separation of compute and storage. Since Hadoop's performance comes from taking advantage of data locality, Hadoop performance can be severaly degraded in the cloud.  By caching intermediate data in-memory and allowing longer pipelines, Spark can amortize expensive reads and writes and maintain good performance for cloud deployments.
