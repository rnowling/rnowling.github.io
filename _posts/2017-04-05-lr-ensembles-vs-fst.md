---
layout: post
title: "Logistic Regression Ensembles vs F<sub>ST</sub> for Insect Population Genetics"
date: 2017-04-05 00:01:19
categories: "bioinformatics"
tags: ["statistics"]
---
In line with my [recent correlation analysis](http://rnowling.github.io/bioinformatics/2017/04/04/cramers-v.html) between Cramer's V and F<sub>ST</sub>, I wanted to compare the weights calculated by the [Logistic Regression Ensembles](http://rnowling.github.io/machine/learning/2017/02/14/lr-gwas.html) I recently discussed with F<sub>ST</sub>. Logistic Regression Ensembles are implemented in the `dev` branch of my population methods exporation toolkit [Asaph](https://github.com/rnowling/asaph/).

I again used the Burkina Faso *An. gambiae* and *An. coluzzii* samples from the [Anopheles gambiae 1000 genomes project](https://www.malariagen.net/projects/ag1000g) for the comparison. I ran the Logistic Regression Ensembles with both the counts and categorical feature encodings and with and without bagging. Plots of the weights from the Logistic Regression Ensembles vs F<sup>ST</sup> scores are below:

![Fst vs LR Ensembles (counts) w/ Bagging](/images/lr-vs-fst/bfm_vs_bfs_fst_vs_lr_counts_bagging.png)

![Fst vs LR Ensembles (counts) w/o Bagging](/images/lr-vs-fst/bfm_vs_bfs_fst_vs_lr_counts_no_bagging.png)

![Fst vs LR Ensembles (categories) w/ Bagging](/images/lr-vs-fst/bfm_vs_bfs_fst_vs_lr_categories_bagging.png)

![Fst vs LR Ensembles (categories) w/o Bagging](/images/lr-vs-fst/bfm_vs_bfs_fst_vs_lr_categories_no_bagging.png)

I used linear regression performed on the F<sup>ST</sup> scores and LR ensemble weights to calculate $$r^2$$ values:

| Feature Encoding | Bagging? | $$r^2$$ |
|:----------------:|:--------:|:-------:|
| Counts           | Yes      | 0.889   |
| Counts           | No       | 0.812   |
| Categories       | Yes      | 0.850   |
| Categories       | No       | 0.643   |


## Conclusion
When bagging is used, Logistic Regression Ensembles weights seem to correlate well with F<sup>ST</sup> scores.  The only real outlier is when Logistic Regression Ensembles are used with the categories feature encoding and no bagging.

It's important to mention that the correlation analysis only tells us how well these methods agree with F<sub>ST</sub>.  They do not necessarily tell us which method is better or worse. Substantial work remains to validate the results of the Logistic Regression Ensembles.
