---
layout: post
title:  "Provisioning Gluster with Ansible"
date:   2015-04-07 9:38:19
categories: devops
tags: [gluster", "ansible", "devops"]
---
With the release of version 1.9, [Ansible](http://www.ansible.com/home) now includes a module named [`gluster_volume`](http://docs.ansible.com/gluster_volume_module.html) for managing [Gluster](http://www.gluster.org/) distributed file system volumes.

The new module is a significant improvement over how I was creating tasks.  Ansible and other configuration management systems aim to describe the state of the systems declaratively so that tasks are idempotent, or executed only once.  This property allows you to run playbooks against a system multiple time without re-running dangerous operations such as re-formatting a file system.  Ansible's modules provide declarative interfaces by incorporating logic for checking the state of the systems and only running an operating when the current state does not match the desired state.

To enable users to perform operations for which there are no modules, Ansible allows shell commands to be specified in playbooks.  Unfortunately, when using shell commands, you are no longer operating in a declarative framework and those particular tasks are not idempotent.  Our playbook tasks for setting up Gluster were, frankly, quite ugly and not very safe.

By using `gluster_volume` module, our playbooks are now idempotent with fewer lines of code.

Since my team uses Gluster for our work, I was quite eager to start using the new module. 

Configuration management systems 
depends on modeling operations so that they are onlyidempotent. 
