---
layout: post
title:  "Apache: Big Data Europe 2015"
date:   2015-10-01 12:13:19
categories: "conferences"
tags: ["apache", "software engineering", "open source"]
---
I had the opportunity to speak at the [Apache: Big Data Europe 2015](http://events.linuxfoundation.org/events/apache-big-data-europe) conference this year and had a wonderful time.

The talks were pretty good overall.  Talks had more content and less hype and marketing than other conferences in the Big Data space that I've been to. Talks were generally by users or developers and generally aimed at other developers, which I liked a lot.  I got to know a larger percentage of the people there and it felt more relaxed.  I definitely prefer that Apache Big Data was smaller and lower-key than larger conferences such as Spark Summit or Strata.

[Pivotal](http://pivotal.io/), [Hortonworks](http://hortonworks.com/), [IBM](http://www.ibm.com/en-us/homepage-a.html), [NASA's JPL](http://www.jpl.nasa.gov/), and [GridGain](https://www.gridgain.com/) were heavily represented, with multiple talks from each company. I'm really impressed by Pivotal, in particular, in their efforts to embrace open source and release projects such as [Apache Geode](http://geode.incubator.apache.org/) and [Apache HAWQ](http://hawq.incubator.apache.org/).  I'm looking forward to playing around with Greenplum when it's open sourced.  I had a great time talking with their developers about Geode and listening to their talks.

[Apache Ignite](https://ignite.apache.org/) and Apache Geode had noticeably different emphases on functionality and their roles in Big Data stacks.  My impression was that Ignite is working to establish itself both as a complement to as well as a replacement for Hadoop, Spark, etc.  The Ignite developers presented on features such as its in-memory file system, caching functionality for secondary file systems, and support for SQL queries.  The broad approach that Ignite is taking turns me off a bit -- it makes it hard for me to evaluate where Ignite truly excels for demanding production workloads versus where it is only able to provide basic capabilities that only look good on paper.

Apache Geode described itself more narrowly, focusing on its role as an in-memory, transactional, distributed key-value store-type database.  Developers and presenters generally emphasized the usage of Geode as part of a stack also including HDFS and some sort of data processing engine.  The Geode talks were relatively down-to-earth and realistic, with rather sophisticated demos.  The Geode approach resonated with me more.

[Netflix](http://www.netflix.com/) and JPL gave great talks on scaling Apache Spark.  Netflix presented on snags their teams ran into and their resulting patches back to the Spark project.  JPL's talk concerned scaling the throughput on Spark streaming using techniques such as operating on raw byte arrays and customized networking code.  Although JPL's final implementation would be unsteady in production due to much higher ingestion than output, their optimization tips and findings are useful.

Due to the overlap between Apache Big Data and ApacheCon: Core, I also meet a few Apache community members outside of Big Data.  I learned a few neat things about CouchBase from [Benjamin Young](one of its developers), had the opportunity to reminisce about Sydney with [Justin Mclean](http://www.classsoftware.com/), and discussed the role of Spark in a neat neuroscience project with Christoph Wille.  I also enjoyed meeting a couple fellow Red Hatters.


Synthetic Data Talk
===================
My talk on synthetic data went very well.  I covered three use cases for synthetic data.  My first use case pertained to an internal system at Red Hat operates on sensitive data.  Using synthetic data enabled us to do development and automated testing without exposing the real data to risk and to build smaller data sets for which calculations could be verified by hand.  My second and third use cases covered BigPetStore Spark and k8petstore and their role in smoke testing infrastructure deployments.  I ended the talk by reviewing the Bigtop Data Generators effort.  You can download my slides [here](/static/rnowling_apache_big_data_eu_2015.pdf).

I had a few people engage with me after the talk, and their feedback and questions are going to be helpful in defining the road map for the Bigtop Data Generators.  Requests and ideas that came out of the conversations included:

* Interest in building similar testing tools based on synthetic data.  Folks want to generate synthetic data, test for correctness of data processing, and load test their systems.
* Generalizing BigPetStore to any type of store -- allow users to create JSON files for generating products
* Tools for training models on real data and using the models to generating synthetic data with similar structure and properties
* Further steps to help users build their own models.  Breaking the data generators into libraries is a great first step for this.  I'm still faced with challenges such as large code bases that are hard to maintain, significant amounts of boilerplate, and the models implementations being hidden deep in the code.  I intend to investigate DSLs and [probabilistic programming](https://en.wikipedia.org/wiki/Probabilistic_programming_language) languages that could help on these fronts.


Apache Bigtop
=============
One of most exciting aspects of the conference was a chance to meet a number of my fellow [Apache Bigtop](http://bigtop.apache.org/) members in-person.  Six active committers and PMC members were able to attend.  We had five talks related to Bigtop in addition to mine:

* [Production secure, high-availability with Puppet](http://events.linuxfoundation.org/sites/events/files/slides/hadoopoflebbe.pdf) by Olaf Flebbe
* [Bigtop docker containers and their usage in our continuous integration system](http://events.linuxfoundation.org/sites/events/files/slides/How%20Bigtop%20Leveraged%20Docker%20for%20Build%20Automation%20and%20%20One-click%20Hadoop%20Provisioning%20ApacheBigData2015EU%20pub.pdf) by Evans Ye
* The history and road map for Bigtop by Nate D'Amico
* A tutorial on using Bigtop for building Big Data distributions by Nate D'Amico
* [Open-source in-memory platforms in Bigtop](http://events.linuxfoundation.org/sites/events/files/slides/Bigtop-IMCS_platforms_0.pdf) by Cos Boudnik

The in-person meetings were very beneficial to Bigtop.  We had a number of impromptu hacking sessions, which gave me a chance to learn more about the larger Bigtop project and its infrastructure. We had opportunities to discuss issues such as migrating our CI nodes.  And we were able to bounce ideas off each other for workshops and future efforts such as tools for deploying Bigtop on OpenStack.

I'm very much looking forward to attending future Apache: Big Data conferences in both [North America](http://events.linuxfoundation.org/events/apache-big-data-north-america) and Europe.







