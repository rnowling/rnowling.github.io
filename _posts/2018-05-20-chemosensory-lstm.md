---
layout: post
title:  "Exploring LSTMs for Classifying Chemosensory Receptors"
date:   2018-05-20 12:13:19
categories: "bioinformatics"
tags: ["deep learning", "machine learning", "statistics", "bioinformatics"]
---

In my recent work on differentiation in _Anopheles gambiae_ populations, chemosensory receptors have proven to be one of the most prominent gene families. These receptors are closely tied to differences in food, mating, and habitation preferences, so this makes sense. In fact, significant losses and gains in chemosensory receptor genes are seen across insect species. This makes these receptors a fascinating and useful object of study for better understanding molecular evolution of insects.

Once my current research project is completed, I'm considering spending some time focusing on chemosensory receptors next.  Insect chemosensory receptor are highly divergent and tend to be difficult to identify in genomes.  Researchers often have to resort to a laborious manual annotation processes for each genome -- a major impediment. I believe that gene-specific gene prediction tools could improve the detection and annotation of these receptors.

[Long Short-Term Memory](https://en.wikipedia.org/wiki/Long_short-term_memory) deep learning techniques are an exciting alternative to established techniques such as [Hidden Markov Models (HMMs)](https://en.wikipedia.org/wiki/Hidden_Markov_model).  I decided to begin exploring LSTMs with a simpler problem: classifying a sequence as either an olfactory (OR) or gustatory (GR) receptor.

I used a data set of 930 ORs and 844 GRs from multiple species of _Drosophila_ and three mosquitoes (_Anopheles gambiae_, _Culex_, and _Aedes aegypti_), with 70% used for the training set and 30% used for the test set.  I vectorized the sequences with each amino acid represented by a one-hot encoded vector.  Based on the examples in [Keras](https://keras.io/), I used a relatively simple LSTM model with a single LSTM layer of 64 units, a dropout rate of 0.2, and a recurrent dropout rate of 0.2 followed by a dense layer with a sigmoidal activation function.

After 50 epochs, I achieved an accuracy of 96.4% -- pretty good for a simple, out-of-the-box model.

I see two main advantages of the LSTM model compared with a profile HMM.  First, the LSTM doesn't require aligning the sequences.  At a basic level, skipping the multiple-sequence alignment (MSA) saves a step.  More importantly, a poor-quality MSA (which can easily happen with poorly-conserved gene families like insect chemosensory receptors) could lead to poorly-performing models.  Skipping the alignment could lead to higher-quality models.  The most impressive part (at least to me), however, is that Keras is a generic machine learning framework that is performing reasonably well on a problem that normally requires the use of domain-specific techniques and software.

The main disadvantage of the LSTM is simple -- how do I improve it?  I'm relatively new to deep learning, so I haven't yet developed the proper intuition for how to design more sophisticated models.  My experience with classical machine learning suggests that hyper-parameter tuning only gets you so far and only gives you a one-time improvement.

I'm guessing that building more sophisticated models, either by adding more layers to the model or even multiple models outputting to the same dense layer, might be more powerful.  In image processing applications, it is generally thought that deep learning layers learn different levels of features.  Why wouldn't the same be true of protein-sequence classification problems?  We generally talk about proteins as having levels (primary, secondary, and tertiary) of structures.  Would using multiple layers of LSTMs or even [convolutional neural networks (CNNs)](https://en.wikipedia.org/wiki/Convolutional_neural_network) to learn protein structure lead to improved the classification accuracy?  Or, given the small-ish data set, are we better off with a hybrid model that combines a LSTM with hand-engineered features?

I see this problem leading to plenty of future work with which to keep myself busy.
