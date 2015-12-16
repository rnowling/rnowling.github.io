---
layout: post
title:  "Imputing Missing Data and Random Forest Variable Importance Scores"
date:   2015-12-15 18:33:00
categories: "machine learning"
tags: ["math", "machine learning"]
---
Missing data is a common problem in math modeling and machine learning.  I've faced the problem of missing data most recently in my Ph.D. work applying Random Forests to variable selection in insect genomes.  Insect genomes tend to be hard to sequence and assemble due multiple factors such as low sample sizes, genetic variances among the samples, and lack of evolutionary-close reference genomes.  Bioinformatics certainly isn't the only use case where data scientists must make decisions about how to handle missing data; you can easily imagine working with survey data where users don't fill out all of the fields.  In fact, recommendation systems can be seen as the "big daddy" of all missing data problems.


Why is Missing Data a Problem?
==============================
Before we cover how to handle missing data, we should discuss why missing data is a problem and whether we should explicitly handle it.  Machine learning models generally have three purposes: (1) predicting an outcome variable based on input variables, (2) explaining an outcome based on input variables, and (3) generating data similar to the input data.

For predictive and explanatory models, the main concern is that model can over fit, causing it to make predictions based on the missing data.  For example, let's consider the case of building a model for predicting customer renewals for a streaming media service.  If I want to see if there is a relationship based on geographic area, so I would include zip codes or states in my feature set but may not have that data for all customers.  I probably don't want my model to associate a missing zip code with a failure to renew their subscription.

There are also situations where missing data are informative. If I build a model to predict customer renewals for a mobile dating, customers usage of various features may indicate how "engaged" the customer is with the service.  It would then be possible to design an intervention program to encourage customer engagement and increase the chances of a customer renewing their subscription.

In my particular use case, I'm trying to identify genetic markers which best explain differences between two species of mosquitoes that only recently diverged.  Both species are vectors of malaria but they differ in feeding and mating habits, insecticide resistance, and the severity of the particular variant malaria parasite they carry.  Understanding the genetic basis for these differences could have significant practical impact on population control efforts.

I have two goals: (1) I want to avoid over fitting in my model.  I have a very small number of samples (< 20) and a large number of variables (tens of thousands to millions) -- pretty every model is going to over fit in some way, but using missing data as a signal offers no explanatory value whatsoever.  (2) I don't want to exclude genetic markers because data is missing. I'll be identifying a subset of genetic markers for further study by biologists.  I don't want to miss markers on important genes, and the biologists can do additional studies to fill in missing data for a small number of markers.

Since my main focus is variable selection, I created some simulated data to investigate the effect of missing data on variable importance scores.  My simulated data has 12 biallelic SNPs which can have values of {A, A}, {A, T}, or {T, T}.  Note that these are multi-sets -- order doesn't matter but counts do.  As such, I encoded each variable as 2 features giving the numbers of As and the number of Ts for a total of 24 features.  For each individual, the values for the first 3 SNPs are sampled from a uniform distribution -- they are uncorrelated with the labels.  The last 9 SNPs are organized into 3 groups, with 100%, 75%, and 50% probability of being correlated with the output label.

The variable importance scores from 100 simulations of 100 individuals with RFs of 100 trees are as follows:

![](/images/rf_missing_data/rf_missing_data_none.png)

Features 1-6 correspond to the uncorrelated control variables -- they are rarely chosen and have low variable importance scores as expected.  Features 7-12 belong to the 3 SNPs with correlations of 100% with the outcome label -- these are chosen often and have the highest importance scores.  Features 13-18 have some correlation and have medium importance scores.  Lastly, features 19 - 24 are basically uncorrelated and are rarely chosen.

I also simulated missing data.  For each group of 3 SNPs, I removed the data for 0, 10, and 20 randomly chosen individuals.  (The individuals were re-sampled for each SNP.)  The variable importance scores are clearly reduced in line with the number of individuals missing data:

![](/images/rf_missing_data/rf_missing_data.png)

Thus, missing data has a dramatic effect on the results.  I had observed a similar pattern in real data, where SNPs with no missing data had variable importance scores an order of magnitude larger than those with missing data.

Approaches for Handling Missing Data
====================================
Bioinformaticians and machine learning communities have devised a number of approaches for handling missing data.

1. *Filter out variables with missing data.* For variables where a large percentage of samples have missing data, this is a reasonable approach.  You don't have enough data to predict the values for the missing data, and you're likely to over fit or get bad results.  With millions of variables, this can also make the problem more computationally tractable.  Filtering out variables with missing data or low "read quality" is a common pre-processing step in bioinformatics.  You want to do this sparingly, however -- as I said above, any of these variables could have crucial biological importance.

2. *Weight variables with missing data.*  Re-weighting variables based on missing data is a second approach.  These variable can still be used but their likelihood of being chosen is now lower, preventing over fitting or inferring associations that wouldn't be helpful explanations.  [Bamova](http://www.uwyo.edu/buerkle/software/bamova/) and [Enriched Random Forests](http://bioinformatics.oxfordjournals.org/content/24/18/2010.short) are two examples where re-weighting is applied.

   For my use case, re-weighting is the exact opposite of what I want to do.  Random Forests already down weight variables with missing data -- I don't want them to do that.

   As a side note, I also have deep reservations about using multinomial models for estimating "certainty."  Although on the surface this approach seems reasonable; more samples (less missing data) is more likely to capture the population distribution.  In practice, I've found that bamova isn't consistent across runs when sample sizes are increased (even with very large sample sizes) despite samples having the same distribution regardless of sample size.

3. *Modify the Model.* Some models such as Random Forests can be modified to "skip" over missing data. [Sift Science](http://blog.siftscience.com/blog/2015/large-scale-decision-forests-lessons-learned) published a great blog post on adding a third child to nodes in Random Forests to handle missing data.  When missing data is encountered, no prediction is made for the samples with missing data, and the samples are simply forwarded to the next split.  I really like this solution, but it requires changing the underlying model algorithm.  For the sake of maintainability, I want to use standard libraries whenever possible, which led me to rule out this approach for my current use case.

4. *Impute missing values.* We can impute the missing values.  Airbnb published a really nice blog discussing several approaches for [imputing missing values](http://nerds.airbnb.com/overcoming-missing-values-in-a-rfc/). If the number of samples with missing data is relatively small for each variable, we can assume the missing values might be very similar to those of the known values.  We have few options here.  We can use a uni-variate approach (treat each variable as independent) and fill in the missing entries with the mean, median, or mode of the known data.  [Scikit-learn provides this functionality](http://scikit-learn.org/stable/auto_examples/missing_values.html).

   If the values don't have a single dominate value, random sampling could be used.  In this case, we could re-sample the individuals to increase the population size and randomly sample from the known distribution of values so that each individual with missing data is replaced with instances with each possible value in proportion to the known distribution.

   Lastly, we can employ a multivariate approach; basically, use techniques from recommendation systems.  For example, the values could be imputed from the k nearest neighbors as described by [Schwender](http://www.ncbi.nlm.nih.gov/pubmed/22686303).

I decided to experiment with using the mode (most frequent value) and random sampling (but only of the values -- not re-sampling the individuals).  Both approaches produced the same result -- allowing me to recover the variable importance scores as if no data was missing.

![](/images/rf_missing_data/rf_missing_data_imputed_mode.png)

![](/images/rf_missing_data/rf_missing_data_imputed_sampling.png)

Conclusion
==========
Missing data can have a dramatic effect on models that can negatively affect accuracy or the explanatory benefits.  Deciding whether and how to handle missing data is problem specific. For my case, I demonstrated that missing data leads to lower variable importance scores, which isn't desirable.  I described several approaches for handling missing data and experimented with imputing missing values using the mode or random sampling.  Both approaches recovered the desired variable importance scores.

I [posted my code](https://gist.github.com/rnowling/bab32a99ea0cb9575dee) so you can recreate my results.



