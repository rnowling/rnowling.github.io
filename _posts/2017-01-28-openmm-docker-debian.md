---
layout: post
title:  "Running OpenMM in Docker on Debian"
date:   2017-01-28 00:01:19
categories: "molecular dynamics"
tags:
---
[OpenMM](http://openmm.org/) is an open-source and high-quality molecular dynamics library for GPUs and CPUs.  I used OpenMM extensively in my first few years of graduate school.  With a new project potentially on my plate, I decided to go back to OpenMM and refamiliarize myself with it.  The first task was to get OpenMM running on my Debian system, preferably with OpenCL support for the built-in Intel GPU.

I decided to build OpenMM inside a Docker container, mostly so I can track dependencies more easily.

## Installing Docker

Since I'm using Debian Squeeze (testing), some of the packages are in flux.  In particular, the `docker.io` package was been [pulled from testing](https://tracker.debian.org/news/804615).

So, I had to install Docker from the upstream repository using the [official instructions](https://docs.docker.com/engine/installation/linux/debian/).  Unfortunately, the command for adding the repository failed:

```
$ sudo add-apt-repository \
       "deb https://apt.dockerproject.org/repo/ \
       debian-$(lsb_release -cs) \
       main testing"

Traceback (most recent call last):
  File "/usr/bin/add-apt-repository", line 95, in <module>
    sp = SoftwareProperties(options=options)
  File "/usr/lib/python3/dist-packages/softwareproperties/SoftwareProperties.py", line 109, in __init__
    self.reload_sourceslist()
  File "/usr/lib/python3/dist-packages/softwareproperties/SoftwareProperties.py", line 599, in reload_sourceslist
    self.distro.get_sources(self.sourceslist)    
  File "/usr/lib/python3/dist-packages/aptsources/distro.py", line 89, in get_sources
    (self.id, self.codename))
aptsources.distro.NoDistroTemplateException: Error: could not find a distribution template for Debian/stretch
```

Thankfully, a simple workaround was available -- just manually append the repo line to `/etc/apt/sources.list`:

```
$ sudo su -c "echo deb https://apt.dockerproject.org/repo/ debian-stretch main testing >> /etc/apt/sources.list"
```

From there, I was able to follow the rest of the instructions to set up Docker for non-root users.

## Building OpenMM

Once I had Docker up and running, I created a `Dockerfile` to build an image containing the OpenMM dependencies and source code.

You can clone the repository and build the image like so:

```
$ git clone https://github.com/rnowling/openmm-docker.git
$ cd openmm-docker
$ docker build -t rnowling/openmm-docker .
```

The build takes quite a while since OpenMM is a large project.  Once built, you can run the container to get a bash shell:

```
$ docker run -i -t --device /dev/dri:/dev/dri rnowling/openmm-docker
root@874b1411bf01:/openmm-7.0.1#
```

Note the `--device /dev/dri:/dev/dri` flag.  This exposes the GPU to the container, allowing usage of the GPU with OpenCL.   Note that the OpenCL mixed and double precision tests will fail.  If you run one of them manually, you'll see why:

```
root@874b1411bf01:/openmm-7.0.1# ./TestOpenCLHarmonicBondForce mixed
exception: This device does not support double precision
```

I used the open-source [beignet](https://freedesktop.org/wiki/Software/Beignet/) OpenCL driver for Intel GPUs.  Unfortunately, biegnet only supports single-precision floating point operations at the moment.  In the future, I may look into using the proprietary Intel OpenCL drivers.  Generally speaking, many of the force calculations can be done in single precision, but double precision is [needed in some of the integration calculations](https://github.com/pandegroup/openmm/issues/1616#issuecomment-252302777) to minimize errors.

Finally, we can test the image with the included Python wrapper:

```
root@874b1411bf01:/openmm-7.0.1# python -m simtk.testInstallation
There are 3 Platforms available:

1 Reference - Successfully computed forces
2 CPU - Successfully computed forces
3 OpenCL - Successfully computed forces

Median difference in forces between platforms:

Reference vs. CPU: 1.99996e-06
Reference vs. OpenCL: 0.055165
CPU vs. OpenCL: 0.0551638
```

Viola!  OpenMM running in a Docker container.