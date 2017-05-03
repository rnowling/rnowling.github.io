---
layout: post
title: "Talk on Productionizing ML Models"
date: 2017-05-03 00:02:19
categories: "machine learning"
tags: ["engineering"]
---
Last night, I gave a talk titled "Real-World Lessons in Machine Learning Applied to Spam Classification" at the [MKE Big Data](https://www.meetup.com/MKE-Big-Data/) meetup.  In my talk, I used spam classification as a use case for communicating some lessons learnd from my experiences building production machine learning-powered services.  In particular, I wanted to get the point across that modeling and algorithm choices are not independent from the requirements of the production system -- we need to design our models and choose our algorithms while keeping in mind how those choices will impact the resulting production system.

You can grab my slides [here](/static/rnowling_mke_big_data_2017.pdf).  My slides and source code I used to generate my plots are also available on the [MKE BD Talks](https://github.com/MKE-Big-Data/MKE-BD-Talks) GitHub repo.

A few attendees had asked for some additional resources related to the topics.  Martin Zinkevich of Google recently published an excellent guide based on their experiences titled [Rules of Machine Learning: Best Practices for ML Engineering](http://martin.zinkevich.org/rules_of_ml/rules_of_ml.pdf), which I highly recommend. [Vowpal Wabbit](http://hunch.net/~vw/) is a powerful toolkit for online machine learning that incorporates some of the latest algorithms and techniques.
