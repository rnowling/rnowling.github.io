---
layout: post
title:  "Optimizing Duplicate Document Detection in Apache Spark"
date:   2016-03-18 12:13:19
categories: "software engineering"
tags: ["software engineering", "spark", "data science"]
---
Recently, I've been working on detecting duplicate or near duplicate documents.  One of the visible group of users of duplicate detection are [search engines](http://cs.brown.edu/courses/cs253/papers/nearduplicate.pdf).

The core technique for detecting duplicates is straightforward:

1. Represent documents as vectors
2. Compute the distances between all pairs of vectors
3. Return those document pairs whose vectors had distances less than some threshold

The devil is in the details, as usual.  How you preprocess the documents' text and vectorize the documents can significantly impact accuracy. I'm using the same data set and approach I discussed in [my talk](conferences/2016/02/17/spark-summit-east-2016.html) at [Spark Summit East 2016](https://spark-summit.org/east-2016/).

From a performance perspective, most applications use hashing techniques such as [simhash](http://www2007.org/papers/paper215.pdf), which can avoid all-pairs calculations.  I wanted to gain a better understanding of the trade offs between how vectors are weighted, the choices of distance functions, and the resulting distribution of distances, so I chose to not to use hash functions for now.

Thankfully, all-pairs calculations are pleasantly parallel. I was able to accelerate my implementation easily with [Apache Spark](http://spark.apache.org/).

Along the way, I learned a few lessons which may be useful to others:

1. Distances between similar but not duplicate documents are larger with normalized binary occurrence vectors than [term frequency-inverse document frequency](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) vectors.  Whereas the topic modeling work emphasized grouping documents with some similarities, we want to be restrict our predictions to documents that are guaranteed to be similar.  Binary occurrence vectors are a better choice here.

2. *Un-cached* RDDs kill the performance of `RDD.cartesian(...)`.  The Cartesian product of an RDD with itself uses the RDD twice, causing the RDD to be calculated multiple times. By caching my RDD before computing the Cartesian product, I reduced my jobs' run time from 2.5 hours to just 10 minutes!

3. It can be more efficient to use collect then sort than to use `RDD.sortBy(...)` followed by a collect.  In my case, I'm returning tens or hundreds of thousands of pairs of document labels and distances.  These pairs can easily fit into memory and be sorted quickly on the master. The `RDD.sortBy(...)` method required an extra stage of computation and shuffling.

4. When using `RDD.cartesian(...)` on an RDD with itself, you'll get 2 pairs for each pair of documents, including pairs of documents with themselves.  You'll need to follow the `RDD.cartesian(...)` operation with an `RDD.filter()` operation to restrict the resulting pairs.
