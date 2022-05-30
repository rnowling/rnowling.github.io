---
layout: post
title:  "Configuring a Multi-User Spark Cluster with Mesos and Gluster"
date:   2015-04-07 9:38:19
categories: spark
tags: ["spark", "gluster", "mesos", "devops", "zeppelin"]
---
My team at Red Hat is a big proponent of [Apache Spark](https://spark.apache.org/) for data processing and analysis work.  

I haven't seen much information on how Spark is deployed and configured in production.  To help fill that void, I'm sharing some information how we deployed and configured our cluster.  Our cluster is relatively small (< 10 nodes) but our machines are beefy, each with 40 cores, 396GB of RAM, and 12 hard drives in RAID 1 arrays. 

Our software stack is based on [CentOS](https://www.centos.org/), [Mesos](http://mesos.apache.org/) for resource management, Spark, and [Gluster](http://www.gluster.org/) as a distributed file system. We use [Ansible](http://www.ansible.com/home) playbooks for provisioning.

We've recently opened the cluster up to our collaborators. Notebooks have become popular for data science work due to their ease of use for exploratory work and sharing results.  Our team primarily uses Spark from the console via SSH on our head node, but we've been investigating [Apache Zeppelin](https://zeppelin.incubator.apache.org/) to lower the barrier of entry for our colleagues new to Spark.  

We want to prevent users from accessing each others' (potentially) sensitive data or accidentally overwriting each others' files, but we aren't providing an internet-facing service.  We've developed a setup that we believe provides a reasonable level of security. When Spark jobs are submitted to Mesos, they are run as the user who submitted the jobs, allowing us to use the standard Linux user security model. All nodes on the cluster have the same set of users.  For each user, we created private directories owned by those users and unreadable by other users in Gluster.

Unfortunately, Zeppelin doesn't proivde much support multiple users or security out of the box.   We're using several measures to secure Zeppelin.  On the Mesos master, a separate Zeppelin instance is run under each user's account with unique ports.  This allows Zeppelin to submit jobs to Mesos as that user and only that user.  To provide authentication, we run [nginx](http://nginx.com/) as a reverse proxy.  Since Zeppelin uses two ports, forwarding connections is a bit complicated -- nginx must use the same ports.  To accomplish this, we run nginx on a separate machine.  To prevent unauthorized or abusive users from connecting to other users' Zeppelin instances, the machine running the Mesos master and Zeppelin instances is firewalled. For the Zeppelin ports, connections are only accepted from the machine running nginx so that users have to go through the authentication mechanism. For the Mesos ports, we only allow connections from other machines on the cluster, so that abusive users can spoof another account to gain access to private files.  Users are given SSH access for file transfers and console work.  To prevent users from using SSH to circumvent the firewall, we disabled SSH access on the machines running Zeppelin and nginx.

As we get feedback from our users, we'll update our configuration.  For example, other users have reported running Zeppelin instances successfully in [Docker](https://www.docker.com/).  Docker has the advantage of providing namespaces for the networking so that nginx can be run on the same machine as the Docker containers.  I haven't gotten Zeppelin running in Docker working with Mesos yet. If I can solve the issues, it would reduce the number of machines needed for our setup.

We've also found that our setup requires authenticating twice.  Browsers see different ports as different domains and do not share the authenticated state between them.  Other users have used URL rewriting to resolve the port forwarding issues.  I haven't tried URL rewriting yet, but that may offer another improvement to our setup.

Zeppelin and Spark are relatively new technologies.  The best ways to properly deploy and secure clusters for multiple users is still an open topic, at least for those of us who are not system administrators by training.  As I learn more, I'll post here so stay tuned!

