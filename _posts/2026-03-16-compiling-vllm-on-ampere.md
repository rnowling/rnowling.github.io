---
layout: post
title:  "Compling vLLM for Ampere Altra (Max)"
date:   2026-03-16 10:13:19
categories: "mlops"
tags: ["docker", "ampere", "arm64"]
---

In preparation for teaching a class on LLMOps and developing agentic AI systems, I've been experimenting with solutions for serving
LLMs to multiple users efficiently.  I previously wrote a [blog post](https://rnowling.github.io/software-engineering/2025/08/02/serving-llms.html)
about using [Ampere's fork](https://github.com/AmpereComputingAI/llama.cpp) of [llama.cpp](https://github.com/ggml-org/llama.cpp) for
serving smaller LLMs without GPUs.

While [Ollama](https://ollama.com/) and llama.cpp are great for a single user, they are not particularly well suited for solving multiple concurrent
users.  [vLLM](https://vllm.ai/) is another open-source LLM serving engine that is quickly becoming the de facto open-source solution
for production LLM serving.  vLLM uses an architecture designed to maintain throughput for multiple users while capping latency.
vLLM has roots in the academic community, employing techniques like [PagedAttention](https://arxiv.org/pdf/2309.06180) and
smarter scheduling to achieve [more efficient batching](https://www.usenix.org/conference/osdi22/presentation/yu).

## The Problem
That said, I ran into issues getting vLLM to run for CPU inference.  I followed the user guide instructions to install pre-compiled Python
wheels for Arm65.  vLLM would successfully start and warm up the model, but as soon as I made a request, the vLLM engine
would segfault and die.

(I had no problems with the standard instructions for GPU inference using the CUDA backend on the same system.)

## The Solution
I ultimately realized that the precompiled version might be assuming that my processor supports the BF16 instructions.
While the AmpereOne processors do, my Ampere Altra (Max) CPU doesn't.
I was able to get vLLM working by building compiling it from source for my particular system.
Thankfully, the [instructions](https://docs.vllm.ai/en/latest/getting_started/installation/cpu/#build-image-from-source)
for building in Docker make this pretty easy.

```bash
# download the source for a release
$ wget https://github.com/vllm-project/vllm/releases/download/v0.17.1/vllm-0.17.1.tar.gz
# extract it 
$ tar -xf vllm-0.17.1.tar-gz
# change directories
$ cd vllm-0.17.1
# build
$ docker build -f docker/Dockerfile.cpu \
        --tag vllm-cpu-arm64 \
        --target vllm-openai .
# run; replace <hf-token> with your HuggingFace token
$ docker run -d \
             -v ~/.cache/huggingface:/root/.cache/huggingface \
             -p 8000:8000 \
             -e "OMP_NUM_THREADS=32" \
             -e "VLLM_CPU_OMP_THREADS_BIND=nobind" \
             -e "HF_TOKEN=<hf-token>" \
             --name vllm \
             vllm-cpu-env:latest \
             --max-model-len 16384 \
             --max-num-batched-tokens 2048 \
             meta-llama/Llama-3.2-3B-Instruct
# tail logs
$ docker logs vllm -f
```

A few notes:

* Use `OMP_NUM_THREADS` to determine the number of cores to use
* Use `VLLM_CPU_OMP_THREADS_BIND` to pin threads to specific cores for better performance
* Use `max-model-len` to set the maximum context size for a single request
* Use `max-num-batched-tokens` to set the number of tokens per batch.  Default is 512.  Higher values increase throughput at the cost
  of latency.

(I found Red Hat's [summary](https://docs.redhat.com/en/documentation/red_hat_ai_inference_server/3.0/html-single/vllm_server_arguments/index)
of key server arguments as well as the [vLLM config documentation](https://docs.vllm.ai/en/latest/cli/serve/)
useful.)

## Conclusions
I haven't conclusively benchmarked vLLM versus Llama.cpp.  So far, however, I can say that:

1. vLLM using a single NVidia GeForce RTX 5060 TI (16 GB VRAM) scales really well to 32 multiple concurrent connections without a significant
  drop in tokens per second or increase in overall request completion time compared to a single user.
1. vLLM is NOT optimized for CPU.  It is very, very slow.
1. Ampere's Llama.cpp fork provides a 20% speed up over upstream Llama.cpp on CPU.
1. Llama.cpp single-request performance is within a factor of 2-3x of vLLM GPU performance.
1. Neither Llama.cpp version scales well beyond a handful of concurrent connections.

I still need to evaluate Llama.cpp performance using my GPU.  And I'm looking forward to evaluating vLLM performance on AmpereOne M. :)


