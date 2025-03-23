---
layout: post
title:  "Exploring CNNs for Classifying Chemosensory Receptors"
date:   2018-05-21 12:13:19
categories: "bioinformatics"
tags: ["deep learning", "machine learning", "statistics", "bioinformatics"]
---

In my [previous post](/bioinformatics/2018/05/20/chemosensory-lstm.html), I explored using a [Long Short-Term Memory](https://en.wikipedia.org/wiki/Long_short-term_memory) deep learning architecture to classify protein sequences as either olfactory or gustatory receptors.  Check out that blog post for background on insect chemosensory receptors and the data set I'm using.  With my LSTM model, I achieved an accuracy of 96.4% after 50 epochs of training.  I hypothesized that [convolutional neural networks (CNNs)](https://en.wikipedia.org/wiki/Convolutional_neural_network) might be also be a useful model, especially if multiple layers of CNNs were used to correspond to the different levels (primary, secondary, and tertiary) organization within protein structures.

Wang, et al.'s paper on [predicting protein secondary structure using deep convolutional neural fields](https://www.nature.com/articles/srep18962) inspired me to give CNNs a try and proved to be a useful guide.  I decided to start with a single layer 1D convolutional network since that's the easiest to get started with and used the [Keras IMDB CNN example](https://github.com/keras-team/keras/blob/ce4947cbaf380589a63def4cc6eb3e460c41254f/examples/imdb_cnn.py) as a reference.  My final deep learning model consisted of 3 layers:

* 1D convolutional layer with 16 filters and a kernel size of $$11\times20$$ using the ReLU activation function
* 1D global max pooling layer
* Single unit output layer with a sigmoid activation function

After 50 epochs, the CNN model achieved an accuracy of 99.6% -- a noticeable improvement over the 96.4% achieved by the LSTM model.

In their work, Wang, et al. used a window size of 11 residues, since, as they note, $$\alpha$$-helices have an average length of 11 residues.  I experimented with using kernel sizes of 7, 9, and 13 as well, but found that a kernel size of 11 gave me the best performance.

So, how does this work?  My current understanding is as follows: We treat each amino acid in a protein sequence as a categorical variable, so each protein sequence of $$N$$ residues is encoded as a $$N \times 20$$ matrix.  For each amino acid (row), the 1D convolutional layer convolves a $$11\times20$$ kernel with entries from a sliding window (5 positions before and after) to calculate a new value for each position.  We have 16 kernels in our setup, so the output for a single sequence is $$(N, 16)$$.  The global max pooling layer then finds the maximum value across all of thepositions for each kernel, producing a vector of length 16.  This resulting vector is passed into the output layer.

The CNN model is not only more accurate but noticably faster on my Nvidia GTX 1050 Ti.  Training and prediction is completed in 30 s vs 30 minutes for the LSTM.

The model does make me a bit suspicious, however.  The model is not very complex and it's probably disingenuous to even refer to this as "deep learning".  The use of a single 1D convolutional layer suggests to me that the model might be finding motifs found in one type of receptor, but not the other.  I could validate this by pulling out the layer weights.

This promising results with CNNs does suggest that deep learning models might be useful for insect chemosensory receptors.  To make the problem more challenging, I'd like to build a model to differentiate between ORs, GRs, and GPRCs.  Insect ORs and GRs were initially thought to be GPCRs, but these GPCRs have a different signaling mechanism (release of a G Protein).
