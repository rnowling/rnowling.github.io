---
layout: post
title:  "Zero-Shot Spam Classifications with LLMs"
date:   2025-08-09 10:13:19
categories: "software-engineering"
tags: ["education", "ai", "llm", "cs-education", "ampere"]
---

I tend to be a "bottom up" learner.  I've been looking for examples of concrete tasks I can apply LLMs to.  I recently learned about
"prompt routing," a pattern in which prompts are delegated to specialized LLMs.  Although there are multiple ways to approach
prompt routing, LLMs themselves can perform classification of prompts, as illustrated in this [AWS LLM-assisted prompt router example](https://github.com/aws-samples/sample-multi-llm-dynamic-prompt-routing/blob/main/llm-assisted-router/lambda/index.py#L14)
and [blog post from PromptLayer](https://medium.com/promptlayer/prompt-routers-and-modular-prompt-architecture-8691d7a57aee). Back in 2016,
I looked at [spam classification](https://rnowling.github.io/data/science/2016/09/04/comparing-lr-regularization-and-optimizers.html) using
logistic regression with a bag-of-words model.  I thought I would try using an LLM for spam classification and see what happens. I started
with a [zero-shot learning](https://en.wikipedia.org/wiki/Zero-shot_learning) approach in which I used an existing LLM
(in this case, [Llama 3.1 8B Instruct](https://www.llama.com/models/llama-3/)) without training data for the specific task. 

## Prompt Engineering
It turns that using prompting a LLM to classify input is more straightforward than I assumed.  You write a prompt telling the LLM to
classify the appended text into one of several explicitly listed categories.  We can see this in PromptLayer's example prompt:

> You are a general-purpose AI that helps people with questions.
> 
> Given a question, your job is to categorize it into one of three categories:
> 1. self: For questions about yourself, such as "What is your name?"
> 2. news: For news-specific questions, like "What's going on with the election?"
> 3. general: For all other questions.
> Your response should be one word only.

and prompt template in the AWS LLM-assisted prompt router:

> Classify the following question as either 'history' or 'math'. If unsure,
> classify as 'unsure':
>
> {question}
> 
> Classification:

There are a few features of this prompts:

1. The first example tells the LLM it's role.  The second example does not.  I didn't do that in my prompt.
1. The prompt directs the LLM to perform classification (e.g., "categorize [the question] into one of three categories" or "Classify the following question").
1. The class labels are given (e.g., "self", "news", "general").  The model will return these string literals to indicate the predicted class.
1. In the first prompt, the categories are described explicitly to give the model context.  In the second example, the prompt relies purely on the LLM's existing knowledge to interpret the class label strings.
1. The LLMs are given a default option (e.g., "general", "unsure") in case it cannot confidently make a determination.
1. In the first prompt, the LLM is explicitly told to return a single word.

I wrote the following prompt template based on these examples:

> Classify the following email as 'spam' or 'not spam'. If unsure, classify
> as 'unsure':
>
> {email_body}
>
> Classification:

In my testing, the LLM returned a full sentence (e.g., "I would classify this email as 'spam'.").  I added an instruction to return
a single word to make parsing the output easier:

> Classify the following email as 'spam' or 'not spam'. If unsure, classify
> as 'unsure'. In the response, only use the phrases 'spam', 'not spam', or
> 'unsure'.
>
> {email_body}
>
> Classification:

## It Works, Sort Of
I evaluated the classification task using the [trec07p](https://plg.uwaterloo.ca/~gvcormac/treccorpus07/about.html) data set and
aforementioned Llama 3.1 8B Instruct model.

And... it worked. Well, sort of.  It achieved an accuracy of 95%.  (My original logistic regression model achieved an accuracy >99%.)

I was genuinely surprised.  There are a lot of things that need to align.  The LLM needs to understand the desired task.  It needs to
understand what is meant by "spam."  I would have at a loss to come up with a given definition for spam if I had to define it. It needs
to respect the restriction on the output.  And lastly, it needs a way to evaluate its own confidence and fall back to the "unsure"
prediction if its not.

In terms of computationally efficiency, it was a disaster.  While the logistic regression model can train on and classify the emails
on a single CPU in less than an hour, it took ~12 hours to classify 1/5 of the emails with the LLM running on 32 cores.  Now, there
is a lot of room for optimization in my implementation.  For example, I am using the llama.cpp server and a Python client that does
the prompting via REST.  There is overhead to the REST calls.  Further, I'm not using any form of batching (pipelining). Regardless
of those details, however, the logistic regression model only needs to compute a single dot product between two vectors of ~100k values
per email, while the LLM needs to evaluate perform arithmetic on 8-billion parameters.

## Approaches for Improvements
There are a few ways to improve the accuracy of the LLM classifier:

1. Use a model with more parameters.  There are larger ~70 and ~405 billion parameter versions of the Llama models.
1. Use a newer model.  Meta recently released the Llama 4 Scout and Maverick models.
1. Try a different line of models.  There are plenty of other open-weight models to try such as Google's Gemma and Qwen.
1. Few-shot prompting: Provide instances of spam and ham emails in the prompt. Examples and advice from the [Prompt Engineering Guide](https://www.promptingguide.ai/techniques/fewshot)
   and [documentation from ApX](https://apxml.com/courses/python-llm-workflows/chapter-8-prompt-engineering-python/few-shot-prompting-techniques)
   are worth checking out.
1. [Fine tuning](https://en.wikipedia.org/wiki/Fine-tuning_(deep_learning)): [Perform additional training](https://developer.nvidia.com/blog/fine-tuning-small-language-models-to-optimize-code-review-accuracy/)
   on one of the open-weight models to specialize it for the spam classification task. Smaller fine-tuned models can outperform larger,
   untuned models on specialized task, providing for a reduction in compute resources as well as an increase in accuracy.

The first three approaches are generally one-off tasks.  Without changing anything else, optimization by model selection can only be
applied once. And there are a relatively small number of model lines and sizes to choose from. If it works, great!  But if you aren't
able to achieve your desired performance goals, you're going to need to explore option approaches.

The few-shot prompting approach is a really nice middle ground.  Few-shot prompting requires relatively few labeled instances to generate
improvements.  A human can easily and quickly label the handful of data needed.  The approach can be tuned by choosing different instances
as examples or the number of examples (up to a point).  Unfortunately, due to model context length limitations, it may not be possible
to include enough labeled instances for hard problems in the prompt.

Fine tuning is more involved than the other approaches.  Training requires more computational resources than inference.  More data is
needed.  Yet, the payoff can be greater -- higher accuracy with more computationally-efficient smaller models.  A fine-tuned model can
even be [pruned or further quantized](https://blog.premai.io/fine-tuning-small-language-models/), leading to even greater computational
efficiency.  The good news is that even fine tuning requires far less training data than training a model from scratch, including
classical ML models.

I'm excited to try these techniques to see if I can close the gap in accuracy and computational efficiency between the LLM-based and
logistic regression approaches.

## Reflection
LLMs are too computationally expensive for many tasks, but training a custom model from scratch requires a large amount of clean,
labeled data.  Frequently, we don't have clean, labeled data sets.  And, manually labeling data is very labor intensive --
prohibitively so.  In genomics, it's particularly challenging because the objects are interest are very rare relative to the whole
set, so it can be difficult to even find examples.

A hybrid aproach might be feasible, though.  A LLM can be used to filter out a set of candidates from the larger set of data.
It will be computationally expensive, but it only needs to be done once or a few times.  The results will likely be noisy with
incorrect predictions, but the minority class will be enriched so a human can identify representive samples much more quickly.
Once a small but clean data set is prepared, a smaller model can be trained to produce an accurate and computationally-efficient
classifier.
