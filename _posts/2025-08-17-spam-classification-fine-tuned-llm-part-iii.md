---
layout: post
title:  "Spam Classification with a Fine-Tuned LLM, Part III: Model Loading and Setup"
date:   2025-08-17 03:13:19
categories: "machine-learning"
tags: [ai", "llm", "fine-tuning", "spam-classification"]
---

This post is part of my series of fine-tuning a LLM for spam classification.  In the previous posts, I described what I had learned
about the [🤗 Dataset library](/machine-learning/2025/08/17/spam-classification-fine-tuned-llm-part-i.html) and
[tokenization](/machine-learning/2025/08/17/spam-classification-fine-tuned-llm-part-ii.html)
using the [🤗 transformers](https://huggingface.co/docs/transformers/index) library.  In this post, I describe loading pretrained models
and setting up the models for specific tasks using the AutoModel factory classes.

## AutoModel Factory Classes
The [🤗 transformers](https://huggingface.co/docs/transformers/index) library provides factory classes for various tasks:

* Sequence classification: [AutoModelForSequenceClassification](https://huggingface.co/docs/transformers/model_doc/auto#transformers.AutoModelForSequenceClassification)
* Question Answering: [AutoModelForQuestionAnswering](https://huggingface.co/docs/transformers/model_doc/auto#transformers.AutoModelForQuestionAnswering)
* Predicting the Next Sentence: [AutoModelForNextSentencePrediction](https://huggingface.co/docs/transformers/model_doc/auto#transformers.AutoModelForNextSentencePrediction)
* Translation or Summarization: [AutoModelForSeq2SeqLM](https://huggingface.co/docs/transformers/model_doc/auto#transformers.AutoModelForSeq2SeqLM)

All of these classes provide a `from_pretrained()` method that downloads and loads the weights of a specified model such as
[meta-llama/Llama-3.2-1B](https://huggingface.co/meta-llama/Llama-3.2-1B).  Each model architecture has a concrete class implementation
such as [LlamaForSequenceClassification](https://github.com/huggingface/transformers/blob/main/src/transformers/models/llama/modeling_llama.py#L494).
The `from_pretrained()` method acts as a factory that instantiates and returns the correct concrete implementation.

These task-specific classes append one or more layers to the model's neural network architecture.  For example, the
[GenericForSequenceClassification](https://github.com/huggingface/transformers/blob/main/src/transformers/modeling_layers.py#L98)
class adds a [Pytorch Linear layer](https://docs.pytorch.org/docs/stable/generated/torch.nn.Linear.html) with one neuron
for each class.  The neurons apply linear regression-type transformations to the outputs of the last layer of the Llama model for each
class, and the class with the highest score is returned.

The class can be used for both regression and classification tasks.  If only one class is provided, then it is assumed to be a regression
problem.  Otherwise, if there are two or more labels, it is assumed to be classification problem. For training, the
[model uses](https://huggingface.co/docs/transformers/v4.53.3/en/model_doc/llama#transformers.LlamaForSequenceClassification.forward.labels)
a Mean-Square Loss for regression problems and a CrossEntropy loss for classification problems.

The `AutoModelForSequenceClassification` factory class is used like so:

```python
model_name = "meta-llama/Llama-3.2-1B"

model = AutoModelForSequenceClassification.from_pretrained(model_name,
                                                           torch_dtype=torch.bfloat16,
                                                           id2label=id2label,
                                                           label2id=label2id,
                                                           num_labels=len(label2id))
```

The named parameters used above are:

* torch_dtype: If the type is [not specified](https://huggingface.co/docs/transformers/models#model-data-type), the default will be to
  load the model in float32.  The float16 and bfloat16 (for Nvidia GPUs) types reduce memory usage by half.  Specifying the type upfront
  avoids loading the model twice.
* id2label: A dictionary of integer class labels to string class labels.
* label2id: A dictionary of string class labels to integer class labels.
* num_labels: An integer used to indicate the number of outputs for the added linear layer.

## Conclusion
In the next blog post, I'll describe how to configure training and train a model.
