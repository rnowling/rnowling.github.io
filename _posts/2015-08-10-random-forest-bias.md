---
layout: post
title:  "Categorical Variable Encoding and Feature Importance Bias with Random Forests"
date:   2015-08-10 12:13:19
categories: "machine learning"
tags: ["math", "machine learning"]
---
[Random Forests](https://en.wikipedia.org/wiki/Random_forest) are a popular and powerful ensemble classification method.  One of RFs nice features is their ability to calculate the importance of features for separating classes.  Among other applications, RFs' feature importance scores have been used to [identify SNPs associated with higher risk of diseases](http://www.biomedcentral.com/1471-2156/5/32/) in genetic data. 

[Strobl, et al.](http://www.biomedcentral.com/1471-2105/8/25) and [Altmann, et al.](http://bioinformatics.oxfordjournals.org/content/26/10/1340.short) analyzed the variable importance calculated from RFs on categorical variables for bias.  The researchers found that the variable importance of categorical variables was directly correlated with the number of categories -- variables with more categories recieved higher variable importance scores.

Altmann, et al., tested for bias by generated synthetic data. The data had 31 categorical variables with 2 to 32 categories.  For each sample, the value for each categorical variable and the label were sampled from the uniform distribution so that the variables were uncorrelated with the labels and equally uninformative.

I decided to recreate the experiment.  I initially tried encoding the categorical variables using [one-hot encoding](https://en.wikipedia.org/wiki/One-hot).  In this encoding, each category of each variable is modeled as a binary feature, and the features for a single variable are mutually exclusive so that only one can feature in the set can have a value of 1.  For example, if my variable has four categories, I would create four binary features.  I ended up with a feature matrix of 1000 samples and 527 features.  I ran 100 simulations and plotted the distributions of the variable importances.  (For each variable, I combined all of the feature importances into a single distribution.)

![](/images/rf_bias_onehot.png)

I was surprised to find no evidence of bias!  It occurred to me that the authors probably didn't use one-hot encoding.  Rather, the authors probably encoded each categorical variable as a single feature, using an integer value to encode the category.  For example, if a variable has four categories then valid features values would be 0, 1, 2, and 3.  When I re-ran the simulations with the integer encoding, I saw the expected bias.

![](/images/rf_bias_stacked.png)

Feature engineering and encoding are incredibly important to training accurate maching learning models.  Be careful.

(I [posted my code](https://gist.github.com/rnowling/a47f5f61bcf9df61d73e) so you can recreate my results.)



