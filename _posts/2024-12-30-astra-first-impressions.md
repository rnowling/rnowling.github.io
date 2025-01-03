---
layout: post
title:  "First Impressions of the System76 Thelio Astra"
date:   2024-12-30 15:13:19
categories: "system administration"
tags: []
---

I recently purchased a [System76 Thelio Astra](https://system76.com/desktops/thelio-astra-a1-n1/configure) workstation.
It's one of the first workstations available with an [Ampere Altra (Max)](https://amperecomputing.com/products/processors)
ARM64 processor.  It's also very reasonably priced for the amount of compute and RAM you can purchase.

Although the workstation comes with Ubuntu by default, I prefer to use Debian as my Linux distro. 
I've recorded some notes on setting up the workstation here.

## Hardware
I ordered the Astra with the Ampere Altra Max M128-30 128-core @ 3.0 Ghz CPU, 512 GB RAM, a 4 GB NVMe M.2 SSD, and
the 4 GB Nvidia A400 GPU.  According to the support team, the Astra model I recieved uses the
[ASRock ALTRAD8UD2-1L2Q motherboard](https://www.asrockrack.com/general/productdetail.asp?Model=ALTRAD8UD2-1L2Q#Specifications).
This is a change from the model mentioned in the [Phoronix review](https://www.phoronix.com/review/system76-thelio-astra).

The motherboard provides a 1 Gb ethernet port (Intel i210) and 2 SFP28 (Broadcom BCM57414) ports.  The latter can be used for 25 GbE with
an adapter (which System76 included).  Notably, the motherboard only has 4 USB ports which support up to USB 3.1 Gen 1.
They do not support USB 3.1 Gen 2 which offers 10 Gb/s throughput, a bump over the 5 Gb/s throughput provided by USB 3.1 Gen 1.
A PCIe expansion card can probably be used to provide additional USB ports and support for the USB 3.1 Gen 2 protocol. The
motherboard provides 4 PCIe slots.  It should be noted that two of the slots are PCIe 4.0 x16, but the remaining two slots
are x16 (Altra Max) or x8 (Altra) depending on the CPU you order.  Support told me that at one point they ran into issues
in which the graphics card would only work if plugged into the third PCIe slot.  They think this is resolved now, but I
don't plan on testing it in other slots anytime soon, so I can't verify.

In the configuration options, System76 provides an opportunity for adding more NMVe SSD drives.  According to the support team,
this is accomplished using a M.2 PCIe expansion card.  (I recently ordered the [ASUS Hyper M.2 x16 Gen5 PCIe card](https://www.amazon.com/gp/product/B0CKH9FWRQ/)
which I'm using with 4 [8 TB Western Digital Black SSDs](https://shop.sandisk.com/products/ssd/internal-ssd/wd-black-sn850x-nvme-ssd?sku=WDS800T2X0E-00CDD0)
in my AMD Threadripper workstation.)

A note about the case.  The case is nice overall, but it is not as convenient to open as the VSX-R7 Series Mid Tower Chassis used by AMD Threadripper workstation from
[Thinkmate](https://www.thinkmate.com/).  There are two particular issues. First, the Thinkmate case uses bigger screws that
can be screwed both by hand using a texture surface around the circumference of the head as well as a screwdriver.  Secondly,
the Thinkmate case has removal separate side panels, while the System76 Thelio case's top and sides are one unit that comes
off together.  Since the power button is on the removable part, I don't see an obvious way to boot the computer with the case
open should you want to check that a hardware change works before reassembling it.

So far, the machine is pretty quiet.  Server cases generally have much louder fans, so many of the existing Ampere Altra
servers aren't really suitable for use as workstations in an office or home environment.  The Thelio Astra was obviously
designed to be put under or by a desk.

## Support
I've reached out to System76 support a few times and had nothing but very positive experiences.  After I ordered, I wanted to
know the particular motherboard that was used so I could look into options for adding more storage.  After I recieved the
machine, I was very confused when it didn't appear to boot (see below).  Support called me the next business day and quickly
resolved my "problem exists between keyboard and chair" (PEBKAC) issue.

## Booting
Booting the Astra takes a *LONG* time.  I initially thought that my machine wasn't functional because I didn't
see any monitor output and I couldn't get caps, scroll, or num lock to work on the keyboard.  It turns out that it
just takes 10-15 minutes for the system to boot up.

## Installing Debian
I was able to successfully use the [arm64 DVD/USB ISO image](https://cdimage.debian.org/debian-cd/current/arm64/iso-dvd/)
to install Debian 12 (bookworm).  The graphical installer couldn't start X11 successfully.  I had no issues with the
text-based installer.  I was up and running in a few minutes.

After install, X11 started without issue, and I was able to log into a GNOME session.  GNOME was very slow, likely due
to limited support for the Nvidia A400 GPU by the Nouveau open-source driver.  Not a huge issue for me since I intend
to access the machine mostly through SSH.

Note to self: the normal user account created by Debian on install isn't part of the sudo group.  The user needs to be
added to the sudo group with:

```bash
$ adduser rnowling sudo
```

The sudo group, however, is already enabled in the `/etc/sudoers` config file.

## Hibernate / Suspend Problems
I did have an issue resuming the machine once it fell asleep. I disabled the functionality by editing
`/etc/systemd/sleep.conf.d/nosuspend.conf` to:

```
[Sleep]
AllowSuspend=no
AllowHibernation=no
AllowSuspendThenHibernate=no
AllowHybridSleep=no
```

and restarting the Systemd login service with:

```bash
$ sudo systemctl restart systemd-logind.service
```

## Configuring Networking
Not unique to this machine, the wired ethernet connection seems to need to be manually connected using Network Manager.
To handle that automatically, I installed the `netplan.io` package and followed [instructions in the Debian manual](https://www.debian.org/doc/manuals/debian-reference/ch05.en.html#_the_modern_network_configuration_for_cloud_with_dhcp)
to create the config file `/etc/netplan/50-dhcp.yaml` with the following:

```yml
network:
  version: 2
  ethernets:
    all-en:
      match:
        name: "enP3p5s0"
      dhcp4: true
      dhcp6: true
```

and running 

```bash
$ sudo netplan generate
$ sudo systemctl reload NetworkManager
```

Note that I changed the adapter name pattern from `en*` to `enP3p5s0` since the Thelio Astra has 3 network adapters.
With this change, the machine connects to the network on reboot automatically.

Overall, a great machine so far!
