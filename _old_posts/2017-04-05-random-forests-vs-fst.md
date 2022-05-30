---
layout: post
title: "Random Forests vs F<sub>ST</sub> for Insect Population Genetics"
date: 2017-04-05 00:01:19
categories: "bioinformatics"
tags: ["statistics"]
---
For my last comparison, I'll look at the correlation between the variable importance measures (VIM) computed by Random Forests vs the scores calculated via F<sub>ST</sub>.  Previously, I analyzed the correlations between F<sub>ST</sub> scores and associations computed using [Cramer's V](http://rnowling.github.io/bioinformatics/2017/04/04/cramers-v.html) and weights computed from [Logistic Regression Ensembles](http://rnowling.github.io/bioinformatics/2017/04/05/lr-ensembles-vs-fst.html).

In line with my [recent correlation analysis](http://rnowling.github.io/bioinformatics/2017/04/04/cramers-v.html) between Cramer's V and F<sub>ST</sub>, I wanted to compare the weights calculated by the [Logistic Regression Ensembles](http://rnowling.github.io/machine/learning/2017/02/14/lr-gwas.html) I recently discussed with F<sub>ST</sub>. 

I again used the Burkina Faso *An. gambiae* and *An. coluzzii* samples from the [Anopheles gambiae 1000 genomes project](https://www.malariagen.net/projects/ag1000g) for the comparison. I ran trained the Random Forests with both the counts and categorical feature encodings using the `dev` branch of my population genetics methods exporation toolkit, [Asaph](https://github.com/rnowling/asaph/). Plots of the variable importance measures from the Random Forests vs F<sup>ST</sup> scores are below:

![Fst vs Random Forests (counts)](/images/random-forests-vs-fst/bfm_vs_bfs_fst_vs_rf_counts.png)

![Fst vs Random Forests (categories)](/images/random-forests-vs-fst/bfm_vs_bfs_fst_vs_rf_categories.png)


With the counts feature-encoding scheme, linear regression between the Random Forests variable importance measures and F<sup>ST</sup> scores had $$r^2=0.665$$. With the categories feature-encoding scheme, linear regression gave $$r^2=0.656$$.

## Conclusion
Variable importance measures computed for each SNP using Random Forests correlate reasonably well with the F<sub>ST</sub> scores, regardless of the encoding scheme used.  (Random Forests are particularly robust to the choice of encoding scheme.)  It would be interesting to analyze variants where Random Forests and F<sub>ST</sub> give substantially different scores.
