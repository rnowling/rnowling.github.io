---
layout: post
title:  "Spam Classification with a Fine-Tuned LLM, Part II: Tokenization"
date:   2025-08-17 02:13:19
categories: "machine-learning"
tags: [ai", "llm", "fine-tuning", "spam-classification"]
---

This post is part of my series of fine-tuning a LLM for spam classification.  In the [previous post](https://rnowling.github.io/machine-learning/2025/08/17/spam-classification-fine-tuned-llm-part-i.html),
I discussed what I learned about the [🤗 Dataset library](https://huggingface.co/docs/datasets/index).  In this post, I'll describe a few
things I learned about tokenizing text.

## Tokenization
LLM models generally do not process text directly.  Rather, the tokenizer in a LLM maintains a finite set of "seen" character combination.
The characters in the text are mapped to a sequence of categorical variable values using a dictionary.  The [🤗 transformers](https://huggingface.co/docs/transformers/index)
library provides an API for using tokenizers, including downloading
them from the [🤗 Hugging Face Hub](https://huggingface.co/).

The [`AutoTokenizer.from_pretrained()`](https://huggingface.co/docs/transformers/model_doc/auto#transformers.AutoTokenizer)
factory method downloads a model from the 🤗 and loads it using a model-specific class. The `use_fast=True` causes the tokenizer
to use a Rust-based instead of Python-based implementation.

I ran into an issue with the [Meta Llama](https://www.llama.com/) models.  They don't seem to define a padding token. The workaround
seems to be using the end-of-sentence token as the padding token.  This can apparently lead to some problems with text generation
because the model doesn't have a way to indicate that the sequence has ended.  Rather, the generator will keep generating tokens
until the output token limit is hit.

Tokenization is accomplished by calling the tokenizer object like a function. (The tokenizer classes [implement the Python](https://huggingface.co/docs/transformers/en/main_classes/tokenizer#transformers.PreTrainedTokenizer.__call__)
`__call()__` special method.)  There are a few interesting parameters:

* max_length: The maximum sequence length.  The maximum sequence length is limited to the LLM's context window size. (This can be
  accessed through the tokenizer's `model_max_length` attribute.)  Longer max lengths will require more GPU VRAM to store the data.
* truncation: Truncate a tokenized string if it exceeds the max length parameter.
* padding: How to handle sequences shorter than the max length.  By default, the sequences are not padded to the same length --
  you effectively get a "jagged" array.  `True` causes the sequences to be padded to the length of the longest sequence. `"max_length"`
  pads everything to the value of the max length parameter.  Note that the batch size is limited to 1 if the sequences are not padded.
  On the other hand, padding can increase GPU VRAM usage.

I used the following settings for the spam classification task:

```python
tokenizer = AutoTokenizer.from_pretrained("meta-llama/Llama-3.2-1B")

# workaround for error: ValueError: Asking to pad but the tokenizer does not have a padding token.
tokenizer.pad_token = tokenizer.eos_token

def tokenize_dataset(dataset):
    return tokenizer(dataset["text"], max_length=2048, truncation=True)

train_tokenized = train_ds.map(tokenize_dataset, batched=True)
```

The tokenizer process add two new columns to the Dataset object:

* input_ids: A list of integers encoding the sequence of tokens. For example, `[128000, 1966, 220, 19, 14, 914, 14, 2589, 11, 379, ...`.
* attention_mask: A list of 1's and 0's with 0's indicating tokens that are masked. For example, `[1, 1, 1, 1, 1, 1, 1, 1, 1, 1, ...]`.

The two lists are the same length.

## Conclusion
Next, I'll discuss how to load pretrained models and perform further training.
