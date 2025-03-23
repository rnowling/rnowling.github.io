---
layout: post
title:  "Statically Compiling C/C++ Programs on CentOS 7"
date:   2015-05-05 23:41:19
categories: "software engineering"
---
I recently had to compile and install [BEDOPS](http://bedops.readthedocs.org/en/latest/index.html), a bioinformatics program.  While trying to compile BEDOPS, I ran into a few errors:

    /usr/bin/ld: cannot find -lstdc++
    /usr/bin/ld: cannot find -lm
    /usr/bin/ld: cannot find -lc
    collect2: error: ld returned 1 exit status

Since I had `glibc` and `libstdc++` installed, I was confused about why the linker couldn't find the libraries.  It turns out that BEDOPS is compiled statically:

    mkdir -p ../bin && g++ -o ../bin/bedmap -static -s -Wall -pedantic -O3 -std=c++11  objects/NaN.o objects/starchConstants.o objects/starchFileHelpers.o objects/starchHelpers.o objects/starchMetadataHelpers.o objects/unstarchHelpers.o objects/starchSha1Digest.o objects/starchBase64Coding.o -L/bedops/applications/bed/bedmap/src/../../../../third-party/jansson/lib -L/bedops/applications/bed/bedmap/src/../../../../third-party/bzip2 -L/bedops/applications/bed/bedmap/src/../../../../third-party/zlib -iquote../../../../interfaces/general-headers -I/bedops/applications/bed/bedmap/src/../../../../third-party -I/bedops/applications/bed/bedmap/src/../../../../third-party/jansson/include -I/bedops/applications/bed/bedmap/src/../../../../third-party/bzip2 -I/bedops/applications/bed/bedmap/src/../../../../third-party/zlib /bedops/applications/bed/bedmap/src/../../../../third-party/jansson/lib/libjansson.a /bedops/applications/bed/bedmap/src/../../../../third-party/bzip2/libbz2.a /bedops/applications/bed/bedmap/src/../../../../third-party/zlib/libz.a Bedmap.cpp

On CentOS 7, you need to install the static versions of the `glibc` and `libstdc++` libraries to compile C/C++ programs statically.  Installing the packages is simple enough:

    $ sudo yum -y install glibc-static libstdc++-static

So, for future reference, if you get linking errors about missing libraries and you know the libraries are installed, check if you need the static versions.


