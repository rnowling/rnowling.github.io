---
layout: post
title:  "Provisioning Gluster Servers with Ansible"
date:   2015-04-21 22:41:19
categories: devops
tags: [gluster", "ansible", "devops"]
---
With the release of version 1.9, [Ansible](http://www.ansible.com/home) now includes a module named [`gluster_volume`](http://docs.ansible.com/gluster_volume_module.html) for managing [Gluster](http://www.gluster.org/) distributed file system volumes.  The new module offers significant improvements over using shell commands including less code, better error handling, clearer declarative statements, and idempotency, the ability to apply an operation once and only once.

With my recent [patch](https://github.com/ansible/ansible-modules-extras/pull/406) that adds support for multiple Gluster bricks, or backing directories on the storage nodes, I've been able to refactor our Ansible playbooks to use the new `gluster_volume` module.

Setting up Gluster with Ansible is now dead simple:

<script src="https://gist.github.com/rnowling/c3f42e907d0ddefe2b2a.js"></script>

The playbook above does the following:

1. Specifes the bricks, volume name, and number of replicas as variables
2. Adds the Gluster repository to yum
3. Installs the Gluster packages
4. Starts the Gluster service
5. Creates a volume
6. Starts the volume

Creating a volume is a bit complicated and warrants some explanation.  The `bricks` and `cluster` parameters take strings seperated by commas.  (The machines specified in the `cluster` parameter are used for peer probing).The `force` parameters allows us to specify bricks in the same partition as `/` (for use in VMs).  The `present` state tells the `gluster_volume` module to create the volume.  The `run_once` parameter specified that the task is run on only one machine rather than on each machine.

The `gluster_volume` builds the brick list by iterating over each host and then each brick.  For example, the brick list for two hosts and two bricks would be

    host1:/brick1 host2:/brick1 host1:/brick2 /host2:/brick2

 When N replicas are used, Gluster groups every N bricks into a replica.  With 2 replicas, the first replica would consist of `host1:/brick1` and `host2:/brick1` while the second replica would consist of `host1:/brick2` and `/host2:/brick2`.  Thus, we must be careful to coordinate the number of replicas with the number of hosts and bricks.

 In the future, I would like to improve the `gluster_volume` API to make the specification of replica and striping groups more explicit and provide some error checking.
