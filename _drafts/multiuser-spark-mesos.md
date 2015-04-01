---
layout: post
title:  "Multi-User Spark Cluster with Mesos and Gluster"
date:   2015-03-31 12:13:19
categories: "infrastructure"
---
For my team's data science projects, we maintain and use a small (< 10 nodes) [Apache Spark](https://spark.apache.org/) cluster. We've made a few design choices that differ from the popular choices such as using [Apache Mesos](http://mesos.apache.org/) instead of YARN and [Gluster](http://www.gluster.org/) instead of HDFS. I want to describe our configuration and how we've enabled multi-user support for others who might be interested.

Hardware and Software Components
================================
We have 8 nodes, each with 40 cores, 396GB of RAM, and 16 drives organized into 8 RAID 1 (mirroring) arrays.

* Operating System: CentOS 7
* Analytics: Apache Spark
* Scheduler: Apache Mesos
* Storage: Gluster
* Provisioning: Ansible

Software Configuration
======================

Achieving Multi-User Support
============================

* Same users on all machines
* Gluster directories controlled by Linux user permissions
* Mesos runs jobs under submitting user.

