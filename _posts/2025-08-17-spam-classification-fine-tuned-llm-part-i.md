---
layout: post
title:  "Spam Classification with a Fine-Tuned LLM, Part I: Datasets"
date:   2025-08-17 01:13:19
categories: "machine-learning"
tags: [ai", "llm", "fine-tuning", "spam-classification"]
---

After my [previous blog post](https://rnowling.github.io/machine-learning/2025/08/09/zero-shot-spam-classification.html) describing
zero-shot text classification with LLMs, I wanted to try fine tuning a LLM.  I had two specific goals:

* Learn how to use the [🤗 Hugging Face](https://huggingface.co/) ecosystem of libraries.
* Learn how performance of fine-tuned LLMs scaled with the number of training samples.

Along the way, I ran into a number of stumbling blocks while trying to understand the examples.  I intend to document these
in a series of posts, starting with what I learned about the [🤗 Datasets](https://huggingface.co/docs/datasets/index) library.

## 🤗 Datasets Library
The 🤗 Hugging Face ecosystem extensively uses the `Dataset` and `DatasetDict` data structures provided by the
library.  Unfortunately, most of the tutorials don't really demonstrate
the structure of these types or how to construct them for custom data sets.  Rather, most tutorials just use the `load_dataset()`
function to load a pre-prepared data set and then use it without showing what's in it.  For example, from the quickstart guide:

```python
dataset = load_dataset("rotten_tomatoes")
def tokenize_dataset(dataset):
    return tokenizer(dataset["text"])
dataset = dataset.map(tokenize_dataset, batched=True)

...

trainer = Trainer(
    model=model,
    args=training_args,
    train_dataset=dataset["train"],
    eval_dataset=dataset["test"],
    tokenizer=tokenizer,
    data_collator=data_collator,
)

...
```

After some searching around and experimentation, I learned a few things.  The DatasetDict class is used to hold multiple Dataset objects.
By convention these correspond to training, validation, and testing data sets.  The Datasets can be accessed using the `[]` operator with the
appropriate strings.

The Dataset class itself is basically a table.  Rows are indexed by integers, and columns are indexed by name. There are conventions for
the column names:

* text: a single string used to hold text data
* label: an integer-valued class designation for classification problems.
* input_ids: a list of integers if the text has been tokenized
* attention_mask: a list of 1's and 0's indicating if any of the tokens are masked

Loading and printing out the [rotten tomatoes](https://huggingface.co/datasets/cornell-movie-review-data/rotten_tomatoes) data set
illustrates this:

```python
>>> dataset = load_dataset("cornell-movie-review-data/rotten_tomatoes")
DatasetDict({
    train: Dataset({
        features: ['text', 'label'],
        num_rows: 8530
    })
    validation: Dataset({
        features: ['text', 'label'],
        num_rows: 1066
    })
    test: Dataset({
        features: ['text', 'label'],
        num_rows: 1066
    })
})
```

I'm mostly interested in working with custom data sets.  It took me a little digging to figure out how to create my own Dataset objects.
The [🤗 Datasets documentation](https://huggingface.co/docs/datasets/create_dataset#from-python-dictionaries) shows how to create Dataset objects from dictionaries:

```python
from datasets import Dataset
ds = Dataset.from_dict({"pokemon": ["bulbasaur", "squirtle"], "type": ["grass", "water"]})
```

and [Pandas DataFrames](https://huggingface.co/docs/datasets/v4.0.0/en/package_reference/main_classes#datasets.Dataset.from_pandas):

```python
ds = Dataset.from_pandas(df)
```

One common pattern in the [🤗 tutorials](https://huggingface.co/docs/transformers/en/quicktour) is to tokenize the text data in a
dataset object:

```python
def tokenize_dataset(dataset):
    return tokenizer(dataset["text"])
dataset = dataset.map(tokenize_dataset, batched=True)
```

The [map function](https://huggingface.co/docs/datasets/package_reference/main_classes#datasets.Dataset.map) can call the given function
on each record, but it seems to be more common to pass the `batched=True` argument that will call the function on batches.  In that case,
in each call, the function receives a dictionary with a subset of rows from the original Dataset, organized by columns.  The function is
expected to return a dictionary of scalar (if not batched) or list (if batched) values.  The map function will use these values to
overwrite or add new columns to produce a new Dataset object.

```python
>>> from datasets import load_dataset
>>> ds = load_dataset("cornell-movie-review-data/rotten_tomatoes", split="validation")
>>> def add_prefix(batch):
        batch["text"] = ["Review: " + text for text in batch["text"]]
        return batch
>>> ds = ds.map(add_prefix, batched=True)
>>> ds[0:3]["text"]
['Review: compassionately explores the seemingly irreconcilable situation between conservative christian parents and their estranged gay and lesbian children .',
 'Review: the soundtrack alone is worth the price of admission .',
 'Review: rodriguez does a splendid job of racial profiling hollywood style--casting excellent latin actors of all ages--a trend long overdue .']
```

## Creating Datasets for the Spam Data
I used what I learned to create Dataset objects for the spam classification data.  The data are partitioned and stored as Feather files.
The data contain five columns: label (string of "ham" or "spam"), email body, email from address, email to address, and subject. 

```python
dfs = []
for flname in glob.glob(os.path.join(dirname, "*.feather")):
    dfs.append(pd.read_feather(flname))
dataset_df = pd.concat(dfs, ignore_index=False)
dataset_df.rename(columns={"body" : "text", "label" : "text_label"}, inplace=True)

# create numerical labels from string labels
dataset_df["label"] = [label2id[text_label] for text_label in dataset_df["text_label"]]

# only keep the email body and numerical label
dataset_df = dataset_df[["text", "label"]]

train_df, rest_df = model_selection.train_test_split(
                          dataset_df,
                          train_size=args.training_size,
                          stratify=dataset_df["label"],
                          shuffle=True)
_, test_df = model_selection.train_test_split(
                   rest_df,
                   test_size=args.testing_size,
                   stratify=rest_df["label"],
                   shuffle=True)

train_ds = Dataset.from_pandas(train_df)
test_ds = Dataset.from_pandas(test_df)
```

## Conclusion
The Dataset objects are likely a Pandas DataFrame in some ways.  They store a 2D table of data as rows and columns.  They provide functions
for adding and removing columns and vectorized transformation operations.  They also provide support for non-text data like audio or image
data.

