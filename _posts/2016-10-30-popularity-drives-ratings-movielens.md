---
layout: post
title:  "Popularity Drives Ratings in the MovieLens Datasets"
date:   2016-10-30 12:13:19
categories: "data science"
tags: ["data science"]
---
To gain some experience with recommendation systems, I've been exploring different algorithms for recommendations on the [MovieLens](http://grouplens.org/datasets/movielens/) 10M dataset.  My logistic regression-hashing trick model achieved a maximum AUC of 96%, while my user-similarity approach using [k-Nearest Neighbors](http://scikit-learn.org/stable/modules/generated/sklearn.neighbors.NearestNeighbors.html) achieved an AUC of 99% with 200 neighbors and the cosine dissimilarity metric.  As such, I became curious as to how well ranking products by their popularity (number of ratings) would predict users' interactions.  I figured the accuracy wouldn't be great so it would make a good baseline.

Well, I was wrong.  In fact, ranking the movies by their number of ratings achieved an AUC of 98.6%!  

The results raised my suspicions -- what if popularity alone is the dominating factor in the MovieLens datasets?  I would suspect that the users' interests would present more complex behavior.  For example, I would expect users' interests to tend to cluster around genres or a combination there of.  But, this doesn't seem the case.  Rather, we may be seeing a "rich gets richer" behavior -- users are highly likely to watch and then rate a movie simply because it's popular.

To test the role of popularity, I randomly generated a dataset and evaluated the kNN and popularity-ranking methods on it.  The generated dataset has the same number of users, movies, and users who have rated each movie as the original dataset.  I generated the dataset by assigning the same number of ratings per movie as the MovieLens dataset to randomly-chosen users.  Thus, the ratings between movies should only be correlated through their respective popularities.

I then evaluated the kNN and popularity-rankings methods on the MovieLens and randomly-generatedatasets:

![AUC](/images/popularity_drives_ratings_movielens/auc.png)

The kNN and popularity-ranking methods did just as well on the randomly-generated dataset as the real MovieLens dataset.  The kNN method achieved AUCs of 97.5% and 99%, respectively, while the popularity-method achieved AUCs of 98.5% and 98.6%, respectively.

This experiment demonstrates two important points:

1. The MovieLens dataset is dominated by popularity, such that considering popularity alone, to the exclusion of consideration of other trends, is enough to achieve significant performance on recommendations.

2. The KNN and popularity-ranking methods work particularly well on datasets where popularity is a dominant trend.

Unfortunately, this first observation probably means that the MovieLens datasets will not be useful in simulating the performance of recommendation systems in a wide range of real-world contexts.  In particular, the MovieLens is not a good substitute for evaluating algorithms on datasets where users' ratings fall into highly separated clusters.

It seems my next task will be to find new datasets and re-evaluate the methods discussed in my previous blog posts.  In particular, I want to find a dataset in which popularity ranking doesn't work particularly well and see how the kNN and logistic regression-hashing trick methods work in those cases.

The code for the experiments discussed here can be found on [GitHub](https://github.com/rnowling/rec-sys-experiments) under the Apache v2 License.


