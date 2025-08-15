---
layout: post
title:  "Installing Unsloth on Ampere with Nvidia Blackwell"
date:   2025-08-14 10:13:19
categories: "system-administration"
tags: [ai", "llm", "fine-tuning", "machine-learning"]
---

After my [previous blog post](https://rnowling.github.io/machine-learning/2025/08/09/zero-shot-spam-classification.html) describing
zero-shot text classification with LLMs, I wanted to try to get fine tuning working to see how much better performance could be
and how small I could make the models.

Fine tuning is memory-intensive.  I have 2 Nvidia RTX 4060 Ti GPUs with 8 GB of VRAM each.  According to
[Unsloth's guidelines](https://docs.unsloth.ai/get-started/beginner-start-here/unsloth-requirements#approximate-vram-requirements-based-on-model-parameters),
I _might_ be able to fine tune a 11B-parameter model with 8 GB of VRAM using their quantization approach.  Without their approach, I _might_
be able to fine tune a 3B-parameter model.  I figured this is a good time to upgrade to GPUs with 16GB VRAM. I ordered and installed
2 Nvidia GTX 5060 Ti GPUs with 16 GB of VRAM each.

The software setup turned out to be more challenging.  I wanted to use the unsloth stack, in particular, because of the promised
memory efficiency and [documented pipeline](https://docs.unsloth.ai/basics/tutorials-how-to-fine-tune-and-run-llms/tutorial-how-to-finetune-llama-3-and-use-in-ollama)
for fine-tuning a LLama 3 model and exporting it for use in [ollama](https://github.com/ollama/ollama).  Installation of the unsloth stack
was complicated by three factors:

1. CUDA toolkit [version 12.8 or higher](https://docs.unsloth.ai/basics/training-llms-with-blackwell-rtx-50-series-and-unsloth)
is needed for the 50-series GTXs and not all of the unsloth dependencies are releasing binaries with support.
1. I'm running on Ampere and not all libraries released binaries for the platform.
1. Dependency management complications.

Here, I document the steps I took to install the unsloth stack and get the [test_llama32_sft.py](https://github.com/unslothai/unsloth/blob/main/blackwell/test_llama32_sft.py)
test script working.

## Choosing the CUDA Toolkit Version
There do not seem to be [nightly builds](https://download.pytorch.org/whl/nightly/torch/) with CUDA 12.8 for aarch64 as of 2025-08-14.
There are builds for CUDA 12.9, though, so I installed [CUDA Toolkit 12.9.1](https://developer.nvidia.com/cuda-12-9-1-download-archive?target_os=Linux&target_arch=arm64-sbsa&Compilation=Native&Distribution=Ubuntu&target_version=24.04&target_type=deb_network)
from Nvidia.  I also made sure that no other CUDA toolkit versions were installed.

## Setting Up the Virtual Environment

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh && source $HOME/.local/bin/env
mkdir 'unsloth-blackwell' && cd 'unsloth-blackwell'
uv venv .venv --python=3.12 --seed
source .venv/bin/activate
```

Note the [original instructions](https://docs.unsloth.ai/basics/training-llms-with-blackwell-rtx-50-series-and-unsloth) have
backticks rather than single quotes around the directory name.

## Installing Pytorch Nightly
There are no builds of vllm for aarch64.  I followed the
[instructions](https://docs.vllm.ai/en/latest/getting_started/installation/gpu.html#use-an-existing-pytorch-installation)
for building from source using an existing pytorch installation.

To avoid compiling pytorch from source, I installed a pytorch nightly wheel built for aarch64, Python 3.12, and CUDA 12.9.

```bash
uv pip install --prerelease=allow --index-url https://download.pytorch.org/whl/nightly/cu129 torch torchvision torchaudio
```

## Installing vllm from Source

Need to install some dependencies:

```bash
sudo apt install libnuma-dev
```

```bash
wget https://github.com/vllm-project/vllm/releases/download/v0.10.0/vllm-0.10.0.tar.gz
tar -xzvf vllm-0.10.0.tar.gz
cd vllm-0.10.0
python use_existing_torch.py
uv pip install -r requirements/build.txt
uv pip install --no-build-isolation .
```

## Install Triton from Source

```bash
wget https://github.com/triton-lang/triton/releases/download/v3.4.0/triton-3.4.0.tar.gz
tar -xzvf triton-3.4.0.tar.gz
cd triton-3.4.0/
pip install -r python/requirements.txt
pip install .
```

## Install Unsloth

```bash
uv pip install unsloth unsloth_zoo bitsandbytes
```

## Downgrade Transformers
Following the unsloth directions here:

```bash
uv pip install -U transformers==4.52.4
```

## reinstall pytorch
When I tried to run the test script, I recieved an error indicating that only Nvidia and Intel GPUs were supported.  Wait a second...
I tracked the error down to a test function that calls `torch.cuda.is_available()`.  Why wasn't pytorching finding the GPUs? Along the
way, pytorch 2.8.0 with only CPU support was installed.  I need to reinstall the desired version:

```bash
uv pip install --prerelease=allow --index-url https://download.pytorch.org/whl/nightly/cu129 torch torchvision torchaudio
```
## Patch transformers
For some reason, configuration_utils.py in the transformers package seemed to be missing the following:

```python
ALLOWED_LAYER_TYPES = (
    "full_attention",
    "sliding_attention",
    "chunked_attention",
    "linear_attention",  # used in minimax
)


def layer_type_validation(layer_types: list[str]):
    """Check that each entry in `layer_types` are allowed."""
    if not all(layer_type in ALLOWED_LAYER_TYPES for layer_type in layer_types):
        raise ValueError(f"The `layer_types` entries must be in {ALLOWED_LAYER_TYPES}")
```

I manually modified `~/unsloth-blackwell/.venv/lib/python3.12/site-packages/transformers/configuration_utils.py` to add it in.

## Reinstall xformers
When installing unsloth, the xformers packaged was installed.  But the installed version apparently brought along its own
version of pytorch (2.8.0) built only with CPU support.  I had to build xformers from source and reinstall it:

```bash
uv pip uninstall xformers
uv pip install -v --no-build-isolation -U git+https://github.com/facebookresearch/xformers.git@main#egg=xformers
```

```bash
$ python -m xformers.info
...
pytorch.version:                                   2.9.0.dev20250806+cu129
pytorch.cuda:                                      available
gpu.compute_capability:                            12.0
gpu.name:                                          NVIDIA GeForce RTX 5060 Ti
...
```

## It Works!
With these installation steps, I was able to get the test script running successfully.  Sloth reported fine tuning Llama 3.2 1B Instruct
with only ~3 GB of VRAM, much less than the ~10 GB of VRAM used when using the [transformers](https://huggingface.co/docs/transformers/index)
library directly.
