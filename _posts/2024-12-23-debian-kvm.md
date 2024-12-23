---
layout: post
title:  "Notes on Creating Debian VMs"
date:   2024-12-23 12:13:19
categories: "system administration"
tags: []
---

I recently needed to run some virtual machines with Debian as the OS. I wanted to create them to be headless (no console
process), without using a GUI, and with KVM/QEMU.  (I will eventually need to get this working on an arm64 system, none of
which Vagrant, Virtualbox, etc. support.)  I'm recording some notes here for myself since a number of the resources I found
helped point in the right direction but didn't do exactly what I wanted.

## Install KVM/QEMU/libvirt/guest tools
To install the various components:

```bash
$ sudo apt install --no-install-recommends qemu-system libvirt-clients libvirt-daemon-system libguestfs-tools
$ sudo adduser rnowling libvirt
```

## Create Network If Needed
At some point or another, I think I deleted the default network.  Here are some steps for recreating it:

```bash
# virsh net-list --all
# virsh net-define /usr/share/libvirt/networks/default.xml
Network default defined from /usr/share/libvirt/networks/default.xml
# virsh net-autostart default
Network default marked as autostarted
# virsh net-start default
Network default started
# virsh net-list --all
```

## Prepare the Guest Image
Download image:

```bash
wget https://cloud.debian.org/images/cloud/bookworm/latest/debian-12-generic-amd64.qcow2
```

Create the DHCP settings config file called `50-dhcp.yaml`:

```yml
# 50-dhcp.yaml
network:
  version: 2
  ethernets:
    all-en:
      match:
        name: "en*"
      dhcp4: true
      dhcp6: true
```

Copy the original image so that we can don't modify the original:
```bash
$ cp debian-12-generic-amd64.qcow2 debian-12-generic-amd64-prepared.qcow2
```

Create a non-root user that cannot login with a password, add my SSH key,
run ssh-keygen and restart sshd (fails the first time without system keys),
and copy over the DHCP networking config details.

```bash
$ virt-customize -a debian-12-generic-amd64-prepared.qcow2 \
                 --run-command "adduser debian -q" \
                 --run-command "adduser debian sudo" \
                 --ssh-inject debian:file:/home/rnowling/.ssh/id_ed25519.pub \
                 --firstboot-command 'ssh-keygen -A && systemctl restart sshd' \
                 --copy-in 50-dhcp.yaml:/etc/netplan
```

Copy the prepared image to an image to be used for that specific VM instance. The
image needs to be in a directory that the `libvirt-qemu` user can read and write.

```bash
$ sudo cp debian-12-generic-amd64-prepared.qcow2 /var/lib/libvirt/images/debian12-test.qcow
```

Start the virtual machine instance.  The image is imported directly (no install process
is run).  The graphical interface (VNC) and console are disabled.

```bash
$ sudo virt-install --name debian12-test \
                    --memory 8192 \
                    --vcpus 4 \
                    --disk /var/lib/libvirt/images/debian12-test.qcow \
                    --import \
                    --os-variant debian11 \
                    --network default \
                    --graphics none \
                    --noautoconsole
```

## SSH into VM
Once running, we can grab the IP address from the DHCP lease:

```bash
$ sudo virsh net-dhcp-leases default
 Expiry Time           MAC address         Protocol   IP address           Hostname   Client ID or DUID
------------------------------------------------------------------------------------------------------------------------------------------------
 2024-12-23 15:59:31   52:54:00:41:d4:17   ipv4       192.168.122.175/24   -          ff:56:50:4d:98:00:02:00:00:ab:11:e1:1e:c0:68:36:f5:c2:9c
```

and SSH into the VM:

```bash
$ ssh debian@192.168.122.175 
The authenticity of host '192.168.122.175 (192.168.122.175)' can't be established.
ED25519 key fingerprint is SHA256:Nc3Eiu9nxXF56YvdshEb5FDiXkHPV4Oq/hymCvxwuCE.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.122.175' (ED25519) to the list of known hosts.
Linux localhost 6.1.0-28-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.119-1 (2024-11-22) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Mon Dec 23 21:10:41 2024 from 192.168.122.1
debian@localhost:~$
```

## References
I found the following references helpful when figuring out how to get this working.

1. [Creating the default libvirt network](https://wiki.libvirt.org/Networking.html#id1)
1. [Creating Guests with Virt-install](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/7/html/virtualization_deployment_and_administration_guide/sect-guest_virtual_machine_installation_overview-creating_guests_with_virt_install#sect-Guest_virtual_machine_installation_from_Network_location)
1. [Thomas Chung's Notes on Creating VMs from Debian Cloud Images](https://wiki.debian.org/ThomasChung/CloudImage)
1. [Adding an SSH key to a VM image](https://www.cyberciti.biz/faq/how-to-add-ssh-public-key-to-qcow2-linux-cloud-images-using-virt-sysprep/)
1. [Network Configuration for Cloud with DHCP](https://www.debian.org/doc/manuals/debian-reference/ch05.en.html#_the_modern_network_configuration_for_cloud_with_dhcp)
1. [Debian KVM notes](https://wiki.debian.org/KVM)
