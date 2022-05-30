---
layout: post
title:  "Open-Source Spark Notebooks"
date:   2015-03-19 12:13:19
categories: spark
tags: ["spark", "conferences", "zeppelin"]
---
Several of my colleagues from Red Hat and I attended [Spark Summit East](http://spark-summit.org/east) 2015.  [Apache Spark](http://spark.apache.org/) is a powerful distributed compute engine.  Spark enables users to write data pipelines using Scala, Python, Java, and R with the Resilient Distributed Data set (RDD) abstraction.  Spark keeps data in-memory, which provides better performance for iterative algorithms like K-means clustering or PageRank.

However, the real power of Spark is the multi-modal programming model. Users can utilize both functional and SQL programming paradigms for processing data, interweaving the two approaches as desired.  There is no overhead from needing to dump, reformat, and load data as would be necessary for other systems combining distributed systems and relational databases.

Additionally, Spark has support for REPLs, which makes it easier to "play" with Spark and data. However, the command-line REPLs do not support in-process data visualization or saving states.  Users from the Python world (like myself) would be disappointed when comparing the REPLs to [iPython Notebook](http://ipython.org/notebook.html). As data scientists, we spend a lot of time trying to understand the structure of data, discover underlying patterns, and evaluate approaches for extracting meaning.

Many of the talks at this year's Spark Summit emphasized visualization and interactive development. Multiple start ups, including [Databricks](https://databricks.com/), sponsors of Spark, are building products built around Spark but targeted at data scientists and end users.  It's clear the gap between the Spark compute engine and user-needs is going to be an important problem to solve going forward.

But, are the only solutions proprietary?  The answer is no, but it's treated as an open secret.  [Apache Zeppelin](https://zeppelin.incubator.apache.org/), [Spark Notebook](https://github.com/andypetrella/spark-notebook), and [Jove Jypyter](https://github.com/jove-sh/jove-jupyter-frontend) are three promising open-source notebooks for use with Apache Spark with Scala.  [Cloudera](http://blog.cloudera.com/blog/2014/08/how-to-use-ipython-notebook-with-apache-spark/) and others have blogged about integrating the iPython Notebook with Spark.

Realizing the need to empower internal users to build and experiment independently, my team will be evaluating these options going forward.  As we learn more, I'll post updates here.
