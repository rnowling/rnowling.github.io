---
layout: post
title:  "Introducing BigTop Data Generators"
date:   2015-09-13 12:13:19
categories: "math"
tags: ["math", "software engineering", "open source"]
---
For the last year, I've been an active contributor to [Apache BigTop's](http://bigtop.apache.org/) BigPetStore, realistic application blueprints for the big data ecosystem.  At the core of BigPetStore is the [BigPetStore Data Generator](https://github.com/rnowling/bigpetstore-data-generator/blob/master/bdcloud_paper/latex/paper.pdf) which simulates customer purchasing behavior to generate realistic but synthetic transaction records.

Over time, we began to realize the far-reaching applicability of data generators beyond BigPetStore.  Applications which operate on sensitive data are a good example.  Synthetic data can be used for work on personal machines and automated CI systems without compromising the real data. For more details on the [advantages of synthetic data](http://sched.co/3ztl), please check out my upcoming talk at [Apache Big Data Europe 2015](http://events.linuxfoundation.org/events/apache-big-data-europe).

With that in mind, I'm pleased to announce a new top-level component of [Apache BigTop](http://bigtop.apache.org/), BigTop Data Generators.  BigTop Data Generators will be a resource for the open-source community, providing both data generators as easily-consumable libraries and tools for creating new data generators.  We welcome any and all contributions.

At present, this includes the BigPetStore Data Generator and several reusable components we've extracted as libraries:

* Samplers: library of interfaces and classes for samplers, probability density functions, and Markov models
* Name Generator:  A CLI tool and library for sampling names from U.S. Social Security Administration data sets
* Location Data Set: U.S. zipcodes, their GPS coordinates, median household incomes, and populations are provided as a library with a reader and data model classes.

We're not stopping there.  We're working on adding new components such as BigTop Weatherman, a weather simulation library, in addition to continued efforts to extract and share components wherever possible.

We're looking forward to seeing how others will use BigTop Data Generators and how the project will evolve over time.












