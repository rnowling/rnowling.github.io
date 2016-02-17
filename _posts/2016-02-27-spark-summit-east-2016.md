---
layout: post
title:  "Spark Summit East 2016"
date:   2016-02-27 12:13:19
categories: "conferences"
tags: ["apache", "software engineering", "open source", "spark", "data science"]
---
I was fortunate to have the opportunity to speak at the [Spark Summit East 2016](https://spark-summit.org/east-2016/) conference this year and had a wonderful time.

Insights into Customer Behavior from Clickstream Data
=====================================================
I combined topic modeling of Red Hat's knowledge base articles with customer page views to analyze customers' topic interests and viewing patterns.  In my talk, I reviewed my topic modeling pipeline, pipeline for aggregating the clickstream data and projecting it onto the topics, and lessons I learned.  I evaluated the resulting topics in terms of their interpretability, composability, and distinctiveness.  One of my more surprising results was that clustering the articles with K-means and extracting topics by identifying enriched words in each cluster gave better topics than using the more popular method, LDA.

Some of the other lessons learned included:

* Consider custom Hadoop input formats when the standard readers aren't adequate.  For example, we needed to make the file readers aware of escaped newline characters.
* Use Parquet for intermediate storage, especially to separate very computationally and memory intensive components of the pipelines from later stages.  Parquet enables Spark to only load the data necessary for the computation, especially when used with the optimizations provided by DataFrames
* When using K-means, perform multiple runs and use more iterations (e.g. 100) than the default settings suggest.  Doing so produces better clusterings and consistency between runs of the pipeline.
* Explicitly un-persist cached RDDs and broadcast variables when you know they won't be needed again.  This can prevent memory leaks and lead to greater performance versus waiting for Spark to evict cached data when it runs out of memory.
* Trust but verify.  Popular methods may not work well for your use case.

You can access my slides [here](/static/rnowling_spark_summit_east_2016.pdf).



