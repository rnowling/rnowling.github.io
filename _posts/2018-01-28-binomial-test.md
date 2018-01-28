---
layout: post
title:  "Testing for Regions of Enriched Differentiation along the Chromosome using the Binomial Test"
date:   2018-01-28 12:13:19
categories: "bioinformatics"
tags: ["math", "machine learning", "statistics", "bioinformatics"]
---
Within my work on insect vector population genetics, we often want to infer regions of the chromosomes that are undergoing differentiation.  One way in which we do this is to look for windows with more than expected number of statistically-significant SNPs.

To set up the test, we first need to perform association tests on each individual SNP using something like the [likelihood-ratio test](/machine/learning/2017/10/07/likelihood-ratio-test.html) or [$$F_{ST}$$](https://en.wikipedia.org/wiki/Fixation_index) to identify SNPs that are strongly correlated with the population structure or phenotype of interest.  We then divide the chromosome into non-overlapping windows and count the number of SNPs in each window. Lastly, we perform a statistical test on each window, with a null hypothesis that the SNPs are uniformly distributed across the windows.

[Neafsey, et al.](http://science.sciencemag.org/content/330/6003/514) performed this analysis using the popular $$\chi_2$$ test.  I prefer using the one-tailed [binomial test](https://en.wikipedia.org/wiki/Binomial_test), however, as it's more sensitive.  Conveniently, the binomial test is available in [Scipy](https://docs.scipy.org/doc/scipy-0.14.0/reference/generated/scipy.stats.binom_test.html).

My script for performing this analysis is available below:

<script src="https://gist.github.com/rnowling/bfd94f606144731233c897d977121146.js"></script>
