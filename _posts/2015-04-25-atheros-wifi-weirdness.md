---
layout: post
title:  "Resolving Atheros Wifi Weirdness on CentOS 7"
date:   2015-04-25 16:31:19
categories: infra
tags: ["centos"]
---
This weekend, I bought a [Dell Inspiron 3847](http://www.bestbuy.com/site/dell-inspiron-desktop-intel-core-i5-12gb-memory-2tb-hard-drive-black/2151038.p?id=1219530045391&skuId=2151038) for use as personal server.  The machine comes with a quad-core Intel i5, 12 GB (upgradable to 16GB) of RAM, a 2TB 7200 RPM hard drive (with space for up to 4 SATA drives), and a PCI Express x16 port. Given its decent specs out of the gate and support for expansion, it's reasonably-priced at $600.

Unfortunately, I spent the afternoon debugging an odd wifi issue with CentOS 7.  I was able to access and use the wifi card during the installation from the `CentOS-7-x86_64-DVD-1503-01.iso`, but not when I rebooted the machine.  Simply put, the wireless card didn't show up at all in the output of `ifconfig` or in the list of NetworkManager connections.

The Inspiron comes with a Dell Wireless 1705, which shows up as an Atheros AR9565:

    $ sudo lspci -k
    ...
    03:00.0 Network controller: Qualcomm Atheros QCA9565 / AR9565 Wireless Network Adapter (rev 01)
	Subsystem: Dell Device 020c
	Kernel driver in use: ath9k
    ...

I haven't able to diagnose why this happened, but I found a workaround: install CentOS 7 using the network rather than the install media. I booted the installer from the same ISO as before, but I installed the packages from the CentOS mirros rather than the ISO. Excellent directions for changing the install source can be found [here](https://www.liberiangeek.net/2014/07/install-centos-7-via-netinstall/). After installing from the network, my wifi card was found and worked without issues.

