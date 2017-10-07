---
layout: post
title:  "Testing Feature Significance with the Likelihood Ratio Test"
date:   2017-10-07 12:13:19
categories: "machine learning"
tags: ["math", "machine learning", "statistics"]
---
[Logistic Regression](https://en.wikipedia.org/wiki/Logistic_regression) (LR) is a popular technique for binary classification within the machine learning and statistics communities.  From the machine learning perspective, it has a number of desirable properties.  Training and prediction are incredibly fast. When using stochastic gradient descent and its cousins, LR supports online learning, enabling models to change as the data changes and training on datasets larger than the available memory on the machine.  And finally, LR naturally accomodates sparse data.

Because of its roots in the statistics community, Logistic Regression models are amenable to analyses other machine learning techniques are not.  The [Likelihood-Ratio Test](https://en.wikipedia.org/wiki/Likelihood-ratio_test) can be used to determine if the addition of the features to a LR model result in a statistically-significant improvement in the fit of the model.<sup>[1](#hosmer)</sup>

I originally learned about the Likelihood-Ratio Test when reading about ways that variants are found in genome-wide association studies (GWAS).  The statistician [David J. Balding](https://en.wikipedia.org/wiki/David_Balding) has significantly impacted the field and its methods.  His [tutorial on statistical methods for population association studies](http://www.montefiore.ulg.ac.be/~kvansteen/GBIO0009-1/ac20112012/Class4/Balding2006.pdf) is a great place to start for anyone interested in the subject.

As Prof. Balding points out, many GWA studies use the Likelihood-Ratio Test to perform single-SNP association tests.  Basically, a LR model is built for each SNP and compared to a null model that only uses the class probabilities.  SNPs with small p-values are then selected for further study.

## Likelihood-Ratio Test
The question we are trying to answer with the Likelihood-Ratio Test is:

> Does the model that includes the variable(s) in question tell us more about the outcome (or response) variable than a model that does not include the variable(s)?

Using the Likelihood-Ratio Test, we compute a p-value indicating the significance of the additional features.  Using that p-value, we can accept or reject the null hypothesis.

Let $$\theta_0$$ and $$\theta_1$$ be the features used in the null and alternative models, respectively.  Note that we need $$\theta_0 \subset \theta_1$$, meaning that the models are "nested."  Let $$x$$ be the feature matrix, $$y$$ be the vector of class labels, $$N$$ denote the number of samples, and $$df$$ be number of additional features in $$\theta_1$$.

The Logistic Regression model is given by:

$$
\pi_\theta(x_i) = \frac{e^{\theta \cdot x_i}}{1+e^{\theta \cdot x_i}}
$$

Note that the intercept is considered part of $\theta$.  We append a columns of 1s to $$x$$ to model the intercept. (In the implementation below, since you control the feature matrices and model, you can model it as you need.)

The likelihood for the Logistic Regression model is given by:

$$
L(\theta | x) = \prod_{i=1}^N h_\theta(x_i)^{y_i} (1 - h_\theta)^{1 - y_i} \\
\log L(\theta | x) = \sum_{i=1}^N y_i \log h_\theta(x_i) + (1 - y_i) \log (1 - h_\theta(x_i))
$$

The Likelihood-Ratio Test is then given by:

$$
G = -2 (\log L(\theta_0 | x) - \log L(\theta_1 | x))
$$

Finally, we compute the p-value for the null model using the $$\chi^2(df)$$ CDF:

$$
p = P[\chi^2(df) > G]
$$

## Python Implementation and Example
Using [scikit-learn](http://scikit-learn.org/stable/) and [scipy](https://www.scipy.org/), implementing the Likelihood-Ratio Test is pretty straightforward (as long as you remember to use the **unnormalized** log losses and negate them):

<script src="https://gist.github.com/rnowling/ec9c9038e492d55ffae2ae257aa4acd9.js?file=likelihood_ratio_test.py"></script>

The `likelihood_ratio_test` function takes four parameters:

1. Feature matrix for the alternative model
2. Labels for the samples
3. A LR model to use for the test
4. (Optional) Feature matrix for the null model.  If this is not given, then the class probabilities are calculated from the sample labels and used.

and returns a p-value indicating the statistical significance of the new features.

To illustrate its use, I generated some fake data with 20 binary features.  The binary features range in their probability of matching the class labels from 0.5 (uncorrelated) to 1.0 (completely correlated).  Half of the features have inverted values (`1 - label`).  I generated 100 fake data sets with 100 samples each.  I then ran the Likelihood-Ratio Test for each feature individually and created a box plot of the p-values:

![](/images/likelihood_ratio_test_p_values_boxplot.png)

As expected, the statistical significance varies according to the probability that the feature matches the label.  And we so no difference in whether the features matches the label or is inverted, also as expected.

(I [posted my code](https://gist.github.com/rnowling/ec9c9038e492d55ffae2ae257aa4acd9) under the Apache License v2 so you can re-create my results and use the test in your own work.)


<a name="hosmer"></a>Note: the derivation given here comes from *Applied Logistic Regression* (3<sup>rd</sup> Ed.) by Hosmer, Lemeshow, and Sturdivant.
