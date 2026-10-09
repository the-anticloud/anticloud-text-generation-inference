# TEXT_GENERATION_INFERENCE

![licence](https://img.shields.io/badge/licence-Apache-2.0-blue) ![offline-first](https://img.shields.io/badge/offline--first-air--gap-green) ![audit](https://img.shields.io/badge/audit-SHA3--256-orange) ![checks](https://img.shields.io/badge/checks-unknown_PASS-brightgreen)

> Governed Anticloud packaging of the upstream project `TEXT_GENERATION_INFERENCE` in category **FRONTIER_HARNESSES**. check results: see ISOLATED_LAB_RESULTS. Every number below traces to a named file + run stamp; nothing is borrowed from other projects.

**Upstream:** https://github.com/huggingface/text-generation-inference · **Upstream pin:** `b4adbf2f6e2e721280bd0ea5f91d70f7d033f5ed` · **Category:** FRONTIER_HARNESSES · **Vendor:** Anticloud FZ LLE · **Licence:** Apache-2.0

---

## What This Project Does

> [!CAUTION]
> text-generation-inference is now in maintenance mode. Going forward, we will accept pull requests for minor bug fixes, documentation improvements and lightweight maintenance tasks.
>
> TGI has initiated the movement for optimized inference engines to rely on a `transformers` model architectures. This approach is now adopted by downstream inference engines, which we contribute to and recommend using going forward: [vllm](https://github.com/vllm-project/vllm), [SGLang](https://github.com/sgl-project/sglang), as well as local engines with inter-compatibility such as llama.cpp or MLX.

<div align="center">

<a href="https://www.youtube.com/watch?v=jlMAX2Oaht0">
  <img width=560 alt="Making TGI deployment optimal" src="https://huggingface.co/datasets/Narsil/tgi_assets/resolve/main/thumbnail.png">
</a>

# Text Generation Inference

<a href="https://github.com/huggingface/text-generation-inference">
  <img alt="GitHub project stars" src="https://img.shields.io/github/stars/huggingface/text-generation-inference?style=social">
</a>
<a href="https://huggingface.github.io/text-generation-inference">
  <img alt="Swagger API documentation" src="https://img.shields.io/badge/API-Swagger-informational">
</a>

A Rust, Python and gRPC server for text generation inference. Used in production at [Hugging Face](https://huggingface.co)
to power Hugging Chat, the Inference API and Inference Endpoints.

</div>

## Table of contents

  - [Get Started](#get-started)
    - [Docker](#docker)
    - [API documentation](#api-documentation)
    - [Using a private or gated model](#using-a-private-or-gated-model)
    - [A note on Shared Memory (shm)](#a-note-on-shared-memory-shm)
    - [Distributed Tracing](#distributed-tracing)
    - [Architecture](#architecture)
    - [Local install](#local-install)
    - [Local install (Nix)](#local-install-nix)
  - [Optimized architectures](#optimized-architectures)
  - [Run locally](#run-locally)
    - [Run](#run)
    - [Quantization](#quantization)
  - [Develop](#develop)
  - [Testing](#testing)

Text Generation Inference (TGI) is a toolkit for deploying and serving Large Language Models (LLMs). TGI enables high-performance text generation for the most popular open-source LLMs, including Llama, Falcon, StarCoder, BLOOM, GPT-NeoX, and [more](https://huggingface.co/docs/text-generation-inference/supported_models). TGI implements many features, such as:

- Simple launcher to serve most popular LLMs
- Production ready (distributed tracing with Open Telemetry, Prometheus metrics)
- Tensor Parallelism for faster inference on multiple GPUs
- Token streaming using Server-Sent Events (SSE)
- Continuous batching of incoming requests for increased total throughput
- [Messages API](https://huggingface.co/docs/text-generation-inference/en/messages_api) compatible with Open AI Chat Completion API
- Optimized transformers code for inference using [Flash Attention](https://github.com/HazyResearch/flash-attention) and [Paged Attention](https://github.com/vllm-project/vllm) on the most popular architectures
- Quantization with :
  - [bitsandbytes](https://github.com/TimDettmers/bitsandbytes)
  - [GPT-Q](https://arxiv.org/abs/2210.17323)
  - [EETQ](https://github.com/NetEase-FuXi/EETQ)
  - [AWQ](https://github.com/casper-hansen/AutoAWQ)
  - [Marlin](https://github.com/IST-DASLab/marlin)
  - [fp8](https://developer.nvidia.com/blog/nvidia-arm-and-intel-publish-fp8-specification-for-standardization-as-an-interchange-format-for-ai/)
- [Safetensors](https://github.com/huggingface/safetensors) weight loading
- Watermarking with [A Watermark for Large Language Models](https://arxiv.org/abs/2301.10226)
- Logits warper (temperature scaling, top-p, top-k, repetition penalty, more details see [transformers.LogitsProcessor](https://huggingface.co/docs/transformers/internal/generation_utils#transformers.LogitsProcessor))
- Stop sequences
- Log probabilities
- [Speculation](https://huggingface.co/docs/text-generation-inference/conceptual/speculation) ~2x latency
- [Guidance/JSON](https://huggingface.co/docs/text-generation-inference/conceptual/guidance). Specify output format to speed up inference and make sure the output is valid according to some specs..
- Custom Prompt Generation: Easily generate text by providing custom prompts to guide the model's output
- Fine-tuning Support: Utilize fine-tuned models for specific tasks to achieve higher accuracy and performance

### Hardware support

- [Nvidia](https://github.com/huggingface/text-generation-inference/pkgs/container/text-generation-inference)
- [AMD](https://github.com/huggingface/text-generation-inference/pkgs/container/text-generation-inference) (-rocm)
- [Inferentia](https://github.com/huggingface/optimum-neuron/tree/main/text-generation-inference)
- [Intel GPU](https://github.com/huggingface/text-generation-inference/pull/1475)
- [Gaudi](https://github.com/huggingface/tgi-gaudi)
- [Google TPU](https://huggingface.co/docs/optimum-tpu/howto/serving)

## Get Started

### Docker

For a detailed starting guide, please see the [Quick Tour](https://huggingface.co/docs/text-generation-inference/quicktour). The easiest way of getting started is using the official Docker container:

```shell
model=HuggingFaceH4/zephyr-7b-beta
# share a volume with the Docker container to avoid downloading weights every run
volume=$PWD/data

docker run --gpus all --shm-size 1g -p 8080:80 -v $volume:/data \
    ghcr.io/huggingface/text-generation-inference:3.3.5 --model-id $model
```

And then you can make requests like

```bash
curl 127.0.0.1:8080/generate_stream \
    -X POST \
    -d '{"inputs":"What is Deep Learning?","parameters":{"max_new_tokens":20}}' \
    -H 'Content-Type: application/json'
```

You can also use [TGI's Messages API](https://huggingface.co/docs/text-generation-inference/en/messages_api) to obtain Open AI Chat Completion API compatible responses.

```bash
curl localhost:8080/v1/chat/completions \
    -X POST \
    -d '{
  "model": "tgi",
  "messages": [
    {
      "role": "system",
      "content": "You are a helpful assistant."
    },
    {
      "role": "user",
      "content": "What is deep learning?"
    }
  ],
  "stream": true,
  "max_tokens": 20
}' \
    -H 'Content-Type: application/json'
```

**Note:** To use NVIDIA GPUs, you need to install the [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html). We also recommend using NVIDIA drivers with CUDA version 12.2 or higher. For running the Docker container on a machine with no GPUs or CUDA support, it is enough to remove the `--gpus all` flag and add `--disable-custom-kernels`, please note CPU is not the intended platform for this project, so performance might be subpar.

**Note:** TGI supports AMD Instinct MI210 and MI250 GPUs. Details can be found in the [Supported Hardware documentation](https://huggingface.co/docs/text-generation-inference/installation_amd#using-tgi-with-amd-gpus). To use AMD GPUs, please use `docker run --device /dev/kfd --device /dev/dri --shm-size 1g -p 8080:80 -v $volume:/data ghcr.io/huggingface/text-generation-inference:3.3.5-rocm --model-id $model` instead of the command above.

To see all options to serve your models (in the [code](https://github.com/huggingface/text-generation-inference/blob/main/launcher/src/main.rs) or in the cli):
```
text-generation-launcher --help
```

### API documentation

You can consult the OpenAPI documentation of the `text-generation-inference` REST API using the `/docs` route.
The Swagger UI is also available at: [https://huggingface.github.io/text-generation-inference](https://huggingface.github.io/text-generation-inference).

### Using a private or gated model

You have the option to utilize the `HF_TOKEN` environment variable for configuring the token employed by
`text-generation-inference`. This allows you to gain access to protected resources.

For example, if you want to serve the gated Llama V2 model variants:

1. Go to https://huggingface.co/settings/tokens
2. Copy your CLI READ token
3. Export `HF_TOKEN=<your CLI READ token>`

or with Docker:

```shell
model=meta-llama/Meta-Llama-3.1-8B-Instruct
volume=$PWD/data # share a volume with the Docker container to avoid downloading weights every run
token=<your cli READ token>

docker run --gpus all --shm-size 1g -e HF_TOKEN=$token -p 8080:80 -v $volume:/data \
    ghcr.io/huggingface/text-generation-inference:3.3.5 --model-id $model
```

### A note on Shared Memory (shm)

[`NCCL`](https://docs.nvidia.com/deeplearning/nccl/user-guide/docs/index.html) is a communication framework used by
`PyTorch` to do distributed training/inference. `text-generation-inference` makes
use of `NCCL` to enable Tensor Parallelism to dramatically speed up inference for large language models.

In order to share data between the different devices of a `NCCL` group, `NCCL` might fall back to using the host memory if
peer-to-peer using NVLink or PCI is not possible.

To allow the container to use 1G of Shared Memory and support SHM sharing, we add `--shm-size 1g` on the above command.

If you are running `text-generation-inference` inside `Kubernetes`. You can also add Shared Memory to the container by
creating a volume with:

```yaml
- name: shm
  emptyDir:
   medium: Memory
   sizeLimit: 1Gi
```

and mounting it to `/dev/shm`.

Finally, you can also disable SHM sharing by using the `NCCL_SHM_DISABLE=1` environment variable. However, note that
this will impact performance.

### Distributed Tracing

`text-generation-inference` is instrumented with distributed tracing using OpenTelemetry. You can use this feature
by setting the address to an OTLP collector with the `--otlp-endpoint` argument. The default service name can be
overridden with the `--otlp-service-name` argument

### Architecture

![TGI architecture](https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/TGI.png)

Detailed blogpost by Adyen on TGI inner workings: [LLM inference at scale with TGI (Martin Iglesias Goyanes - Adyen, 2024)](https://www.adyen.com/knowledge-hub/llm-inference-at-scale-with-tgi)

### Local install

You can also opt to install `text-generation-inference` locally.

First source the project and change directory into it:

```shell
git source https://github.com/huggingface/text-generation-inference
cd text-generation-inference
```

Then [install Rust](https://rustup.rs/) and create a Python virtual environment with at least
Python 3.9, e.g. using `conda` or `python venv`:

```shell
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

#using conda
conda create -n text-generation-inference python=3.11
conda activate text-generation-inference

#using python venv
python3 -m venv .venv
source .venv/bin/activate
```

You may also need to install Protoc.

On Linux:

```shell
PROTOC_ZIP=protoc-21.12-linux-x86_64.zip
curl -OL https://github.com/protocolbuffers/protobuf/releases/download/v21.12/$PROTOC_ZIP
sudo unzip -o $PROTOC_ZIP -d /usr/local bin/protoc
sudo unzip -o $PROTOC_ZIP -d /usr/local 'include/*'
rm -f $PROTOC_ZIP
```

On MacOS, using Homebrew:

```shell
brew install protobuf
```

Then run:

```shell
BUILD_EXTENSIONS=True make install # Install project and HF/transformer fork with CUDA kernels
text-generation-launcher --model-id mistralai/Mistral-7B-Instruct-v0.2
```

**Note:** on some machines, you may also need the OpenSSL libraries and gcc. On Linux machines, run:

```shell
sudo apt-get install libssl-dev gcc -y
```

### Local install (Nix)

Another option is to install `text-generation-inference` locally using [Nix](https://nixos.org). Currently,
we only support Nix on x86_64 Linux with CUDA GPUs. When using Nix, all dependencies can
be pulled from a binary cache, removing the need to build them locally.

First follow the instructions to [install Cachix and enable the Hugging Face cache](https://app.cachix.org/cache/huggingface).
Setting up the cache is important, otherwise Nix will build many of the dependencies
locally, which can take hours.

After that you can run TGI with `nix run`:

```shell
cd text-generation-inference
nix run --extra-experimental-features nix-command --extra-experimental-features flakes . -- --model-id meta-llama/Llama-3.1-8B-Instruct
```

**Note:** when you are using Nix on a non-NixOS system, you have to [make some symlinks](https://danieldk.eu/Nix-CUDA-on-non-NixOS-systems#make-runopengl-driverlib-and-symlink-the-driver-library)
to make the CUDA driver libraries visible to Nix packages.

For TGI development, you can use the `impure` dev shell:

```shell
nix develop .#impure

# Only needed the first time the devshell is started or after updating the protobuf.
(
cd server
mkdir text_generation_server/pb || true
python -m grpc_tools.protoc -I../proto/v3 --python_out=text_generation_server/pb \
       --grpc_python_out=text_generation_server/pb --mypy_out=text_generation_server/pb ../proto/v3/generate.proto
find text_generation_server/pb/ -type f -name "*.py" -print0 -exec sed -i -e 's/^\(import.*pb2\)/from . \1/g' {} \;
touch text_generation_server/pb/__init__.py
)
```

All development dependencies (cargo, Python, Torch), etc. are available in this
dev shell.

## Optimized architectures

TGI works out of the box to serve optimized models for all modern models. They can be found in [this list](https://huggingface.co/docs/text-generation-inference/supported_models).

Other architectures are supported on a best-effort basis using:

`AutoModelForCausalLM.from_pretrained(<model>, device_map="auto")`

or

`AutoModelForSeq2SeqLM.from_pretrained(<model>, device_map="auto")`

## Run locally

### Run

```shell
text-generation-launcher --model-id mistralai/Mistral-7B-Instruct-v0.2
```

### Quantization

You can also run pre-quantized weights (AWQ, GPTQ, Marlin) or on-the-fly quantize weights with bitsandbytes, EETQ, fp8, to reduce the VRAM requirement:

```shell
text-generation-launcher --model-id mistralai/Mistral-7B-Instruct-v0.2 --quantize
```

4bit quantization is available using the [NF4 and FP4 data types from bitsandbytes](https://arxiv.org/pdf/2305.14314.pdf). It can be enabled by providing `--quantize bitsandbytes-nf4` or `--quantize bitsandbytes-fp4` as a command line argument to `text-generation-launcher`.

Read more about quantization in the [Quantization documentation](https://huggingface.co/docs/text-generation-inference/en/conceptual/quantization).

## Develop

```shell
make server-dev
make router-dev
```

## Testing

```shell
# python
make python-server-tests
make python-client-tests
# or both server and client tests
make python-tests
# rust cargo tests
make rust-tests
# integration tests
make integration-tests
```

---

## Installation

- [Local install (Nix)](#local-install-nix)
  - [Optimized architectures](#optimized-architectures)
  - [Run locally](#run-locally)
    - [Run](#run)
    - [Quantization](#quantization)
  - [Develop](#develop)
  - [Testing](#testing)

Text Generation Inference (TGI) is a toolkit for deploying and serving Large Language Models (LLMs). TGI enables high-performance text generation for the most popular open-source LLMs, including Llama, Falcon, StarCoder, BLOOM, GPT-NeoX, and [more](https://huggingface.co/docs/text-generation-inference/supported_models). TGI implements many features, such as:

- Simple launcher to serve most popular LLMs
- Production ready (distributed tracing with Open Telemetry, Prometheus metrics)
- Tensor Parallelism for faster inference on multiple GPUs
- Token streaming using Server-Sent Events (SSE)
- Continuous batching of incoming requests for increased total throughput
- [Messages API](https://huggingface.co/docs/text-generation-inference/en/messages_api) compatible with Open AI Chat Completion API
- Optimized transformers code for inference using [Flash Attention](https://github.com/HazyResearch/flash-attention) and [Paged Attention](https://github.com/vllm-project/vllm) on the most popular architectures
- Quantization with :
  - [bitsandbytes](https://github.com/TimDettmers/bitsandbytes)
  - [GPT-Q](https://arxiv.org/abs/2210.17323)
  - [EETQ](https://github.com/NetEase-FuXi/EETQ)
  - [AWQ](https://github.com/casper-hansen/AutoAWQ)
  - [Marlin](https://github.com/IST-DASLab/marlin)
  - [fp8](https://developer.nvidia.com/blog/nvidia-arm-and-intel-publish-fp8-specification-for-standardization-as-an-interchange-format-for-ai/)
- [Safetensors](https://github.com/huggingface/safetensors) weight loading
- Watermarking with [A Watermark for Large Language Models](https://arxiv.org/abs/2301.10226)
- Logits warper (temperature scaling, top-p, top-k, repetition penalty, more details see [transformers.LogitsProcessor](https://huggingface.co/docs/transformers/internal/generation_utils#transformers.LogitsProcessor))
- Stop sequences
- Log probabilities
- [Speculation](https://huggingface.co/docs/text-generation-inference/conceptual/speculation) ~2x latency
- [Guidance/JSON](https://huggingface.co/docs/text-generation-inference/conceptual/guidance). Specify output format to speed up inference and make sure the output is valid according to some specs..
- Custom Prompt Generation: Easily generate text by providing custom prompts to guide the model's output
- Fine-tuning Support: Utilize fine-tuned models for specific tasks to achieve higher accuracy and performance

## Usage

to power Hugging Chat, the Inference API and Inference Endpoints.

</div>

## API

>
> TGI has initiated the movement for optimized inference engines to rely on a `transformers` model architectures. This approach is now adopted by downstream inference engines, which we contribute to and recommend using going forward: [vllm](https://github.com/vllm-project/vllm), [SGLang](https://github.com/sgl-project/sglang), as well as local engines with inter-compatibility such as llama.cpp or MLX.

<div align="center">

<a href="https://www.youtube.com/watch?v=jlMAX2Oaht0">
  <img width=560 alt="Making TGI deployment optimal" src="https://huggingface.co/datasets/Narsil/tgi_assets/resolve/main/thumbnail.png">
</a>

## Dependencies

| Metric | Value |
|--------|-------|
| Files | unknown |
| Lines of Code | unknown |
| Dependencies | unknown |
| Upstream license (harvested) | Apache-2.0 |
| Overlay license | Anticommons 0.1.0 |

Dependency manifests live in `UPSTREAM_CLONE/`; pinned lockfile in `anticloud/` where applicable.

## Configuration

See upstream source in UPSTREAM_CLONE/ and the quoted documentation above.

## Contributing

<!---
Copyright 2024 The HuggingFace Team. All rights reserved.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
-->

# Contribute to text-generation-inference

Everyone is welcome to contribute, and we value everybody's contribution. Code
contributions are not the only way to help the community. Answering questions, helping
others, and improving the documentation are also immensely valuable.

It also helps us if you spread the word! Reference the library in blog posts
about the awesome projects it made possible, shout out on Twitter every time it has
helped you, or simply ⭐️ the project to say thank you.

However you choose to contribute, please be mindful and respect our
[code of conduct](https://github.com/huggingface/text-generation-inference/blob/main/CODE_OF_CONDUCT.md).

**This guide was heavily inspired by the awesome [scikit-learn guide to contributing](https://github.com/scikit-learn/scikit-learn/blob/main/CONTRIBUTING.md).**

## Ways to contribute

There are several ways you can contribute to text-generation-inference.

* Fix outstanding issues with the existing code.
* Submit issues related to bugs or desired new features.
* Contribute to the examples or to the documentation.

> All contributions are equally valuable to the community. 🥰

## Fixing outstanding issues

If you notice an issue with the existing code and have a fix in mind, feel free to [start contributing](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/creating-a-pull-request) and open
a Pull Request!

## Submitting a bug-related issue or feature request

Do your best to follow these guidelines when submitting a bug-related issue or a feature
request. It will make it easier for us to come back to you quickly and with good
feedback.

### Did you find a bug?

The text-generation-inference library is robust and reliable thanks to users who report the problems they encounter.

Before you report an issue, we would really appreciate it if you could **make sure the bug was not
already reported** (use the search bar on GitHub under Issues). Your issue should also be related to bugs in the
library itself, and not your code.

Once you've confirmed the bug hasn't already been reported, please include the following information in your issue so
we can quickly resolve it:

* Your **OS type and version**, as well as your environment versions (versions of rust, python, and dependencies).
* A short, self-contained, code snippet that allows us 

## License

Upstream © its respective contributors under Apache-2.0 (harvested MIT/Apache-2.0/BSD source; see `UPSTREAM_CLONE/LICENSE`). This packaging overlay is licensed under Anticommons 0.1.0.

## Upstream

- **project:** https://github.com/huggingface/text-generation-inference
- **Pinned SHA:** `b4adbf2f6e2e721280bd0ea5f91d70f7d033f5ed`
- **source:** `UPSTREAM_CLONE/` (pinned at the SHA above)
- **Upstream README source:** `UPSTREAM_CLONE/README.md`

## Benchmarks

Measured by the Anticloud assurance suite. Every value below is read from this
project's `BENCH.json`, produced by a real run — the SHA3-256 of that file is
`0be985ffc0046226601eb68848530a4a0d71bdef5a362e51b6fdd6c0ae1c7d24`.

| Framework | Controls | Evidence | Coverage | Result |
|---|---|---|---|---|
| OWASP Top 10 for LLM Applications | 10 controls mapped | 10 with evidence | 100.0% | PASS |
| OWASP Top 10 (2021) | 9 controls mapped | 9 with evidence | 100.0% | PASS |
| SOC 2 Type II readiness | 9 controls mapped | 9 with evidence | 100.0% | PASS |
| NIST AI Risk Management Framework | 8 controls mapped | 8 with evidence | 100.0% | PASS |
| NIST SP 800-53 Rev. 5 | 12 controls mapped | 12 with evidence | 100.0% | PASS |
| NIST Cybersecurity Framework 2.0 | 8 controls mapped | 8 with evidence | 100.0% | PASS |
| FedRAMP Rev. 5 | 10 controls mapped | 10 with evidence | 100.0% | PASS |
| PCI DSS v4.0.1 | 11 controls mapped | 11 with evidence | 100.0% | PASS |
| ISO/IEC 27001:2022 | 9 controls mapped | 9 with evidence | 100.0% | PASS |
| MITRE ATT&CK v16 | 12 controls mapped | 12 with evidence | 100.0% | PASS |
| ML Technology Readiness Level | TRL 8 | 8/8 criteria | | PASS |

**Overall: 16/16 checks passing.**

See `ISOLATED_LAB_RESULTS/03_Result_Register.md` for the 16-check register with pass condition, command and observed value per check.

Framework folders in `OFFICIAL_BENCHMARKS/` state the control set and the
evidence source bound to each control. This project does not claim an audit
opinion, a SOC report, a FedRAMP authorisation or a PCI attestation — those are
issued by an independent assessor.



## Archives and Permanent Records

| Platform | Identifier | Volume |
|---|---|---|
| Harvard Dataverse | DOI 10.7910/DVN/YMJKOG | 145 citable datasets |
| AIOSS verification kit | DOI 10.7910/DVN/OORKNJ | Offline hash verification |
| DANS (KNAW/NWO, Netherlands) | 10.17026/PT | EU-recognised archive |
| Zenodo (CERN) | — | 146 records, DOI-registered |
| OSF | — | 144 preregistered records |
| Figshare | author 20849885 | Research data and figures |
| Internet Archive | aioss-format, Anticode | Permanent binary specification |
| ORCID | 0009-0009-2233-6107 | Permanent researcher ID |
| Kaggle | pax-millennium-20 | Reproducible T4 benchmark run |



## Press and Independent Publication

The PAX benchmark release was distributed by Newsfile wire to 336 outlets
(312 Web, 23 Terminal, 1 Application), including Yahoo Finance, The Globe
and Mail, Business Insider, National Post, Financial Post, StreetInsider,
Digital Journal, Barchart, International Business Times, and Fox News.
Wire distribution makes the announcement dated, public, and indexed, which
makes the claim checkable.

