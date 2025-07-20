---
layout: post
title:  "Limiting Parallelism in scikit-learn"
date:   2025-07-19 15:13:19
categories: "parallelism"
tags: []
---

I've recently been running into issues with [KMeans clustering](https://scikit-learn.org/stable/modules/generated/sklearn.cluster.KMeans.html)
on my [System76 Thelio Astra](https://system76.com/desktops/thelio-astra-a1-n1/configure) workstation with 128 cores. Primarily, the
Python interpreter would segfault.  Although the Jupyter web interface didn't provide any hints, the Jupyter notebook console output
the following error message:

```
OpenBLAS warning: precompiled NUM_THREADS exceeded, adding auxiliary array for thread metadata.
To avoid this warning, please rebuild your copy of OpenBLAS with a larger NUM_THREADS setting
or set the environment variable OPENBLAS_NUM_THREADS to 64 or lower
```

Scikit-learn uses three levels of [parallelism](https://scikit-learn.org/stable/computing/parallelism.html) by default:

* Multiple processes with joblib
* Threads with OpenMP
* Threads in the BLAS routines used by Numpy and Scipy

The KMeans class used to have a parameter `n_jobs` that would allow the user to set the number of processes launched.  This parameter
was deprecated in version 0.23 and removed in version 0.25. A new [parallelization scheme](https://github.com/scikit-learn/scikit-learn/pull/11950)
using OpenMP was implemented to provide finer-grained parallelism and improve performance. In effect, the total number of threads may be
as high as `OMP_NUM_THREADS * OPENBLAS_NUM_THREADS`. By default, the new KMeans parallelization process will use one OpenMP thread per
core. Each OpenMP thread may use Numpy or Scipy routines that use a BLAS library like OpenBLAS that in turns spawns its own threads.

When I converted the notebook into a standalone script, the Python interpreter output the following error message before crashing:

```
double free or corruption (out)
Aborted (core dumped)
```

I was looking for a way to reduce the number of OpenBLAS threads.  The `OPENBLAS_NUM_THREADS` and `OMP_NUM_THREADS` environmental
variables are one mechanism but not straightforward to use with Jupyter notebooks and set separately for each notebook. The scikit-learn
documentation suggests using the [`threadpoolctl` library](https://github.com/joblib/threadpoolctl) to control
the number of parallel units at each level. Using threadpoolctl, I was not only able to reduce the number of OpenBLAS threads to
avoid the over-allocation but workaround the interpreter crashes.

Unfortunately, neither the scikit-learn or threadpoolctl documentation are particularly clear on how to use it. I've attempted to
describe how to use the functions of the threadpoolctl library with a little more context.

1. Import your libraries of interest since the linked parallelization libraries are loaded and can be discovered:
   ```python
   import mdtraj
   import numpy as np
   import scipy as sp
   import sklearn
   ```
2. Discover the parallelization libraries being used by running:
   ```python
   from threadpoolctl import threadpool_info
   from pprint import pprint

   pprint(threadpool_info())
   ```

   which will output something like:

   ```
   [{'architecture': 'neoversen1',
     'filepath': '/home/rnowling/openmm-venv/lib/python3.12/site-packages/numpy.libs/libscipy_openblas64_-0f683016.so',
     'internal_api': 'openblas',
     'num_threads': 32,
     'prefix': 'libscipy_openblas',
     'threading_layer': 'pthreads',
     'user_api': 'blas',
     'version': '0.3.27'},
    {'filepath': '/home/rnowling/openmm-venv/lib/python3.12/site-packages/mdtraj.libs/libgomp-d22c30c5.so.1.0.0',
     'internal_api': 'openmp',
     'num_threads': 128,
     'prefix': 'libgomp',
     'user_api': 'openmp',
     'version': None},
    {'architecture': 'neoversen1',
     'filepath': '/home/rnowling/openmm-venv/lib/python3.12/site-packages/scipy.libs/libscipy_openblas-9778f98e.so',
     'internal_api': 'openblas',
     'num_threads': 32,
     'prefix': 'libscipy_openblas',
     'threading_layer': 'pthreads',
     'user_api': 'blas',
     'version': '0.3.28'}]
   ```
3. Use threadpoolctl's context managers to limit the number of threads at each level:

   ```python
   from threadpoolctl import threadpool_limits

   with threadpool_limits(limits=8, user_api="openmp"):
       with threadpool_limits(limits=4, user_api="blas"):
           ...
   ```

   This code snippet will limit OpenMP to spawning 8 threads and OpenBLAS to spawning 4 threads.

The `threadpool_limits` context manager parameters are interpreted as follows:

  * `limits`: The maximum number of parallel execution units (e.g., threads)
  * `user_api`: The name of a library type given in the `user_api` field of the discovery output.
