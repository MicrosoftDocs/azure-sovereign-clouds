---
title: "Model reference for Foundry Local on Azure Local"
titleSuffix: Foundry Local on Azure Local
description: "Reference specifications for generative small language models in Foundry Local on Azure Local, including GPU requirements, recommended settings, and performance benchmarks."
ms.service: azure
ms.subservice: sovereign-private-clouds
appliesto:
- Foundry Local on Azure Local
ms.topic: reference
ms.author: cwatson
author: cwatson-cat
ms.date: 04/17/2026
ai-usage: ai-assisted
---

# Model reference for Foundry Local on Azure Local

This article provides GPU requirements, recommended settings, and expected performance benchmarks for each model in the Foundry Local catalog.

[!INCLUDE [foundry-local-preview](includes/foundry-local-preview.md)]

For a list of available models and guidance on choosing one, see [Generative small language models in Foundry Local on Azure Local](concept-models.md).

## Microsoft

### Phi-3-mini-128k-instruct

Natively-accelerated GPU generation (**recommended**): `Ampere (SM 8.0)` or higher

Natively-supported GPU generation: `Ampere (SM 8.0)` or higher

Minimal supported GPU generation: `Volta (SM 7.0)`

**NVIDIA A10 Tensor Core GPU (Ampere architecture, SM 8.0)**

The following table shows the recommended settings and expected running times for this GPU type:

| Setting | Value |
|---|---|
| Recommended GPU utilization | 0.85 |
| Max model context length | 29,472 |
| Required memory | 8.428 GB |

**Expected running times**

The following table provides performance metrics for standard chat completion usages:

| Maximal concurrency | Mean TTFT (ms)** | P99 TTFT (ms)** | Output throughput (tokens/s) |
|---|---|---|---|
| 1 requests | 40.94 | 84.03 | 55.73 |
| 2 requests | 57.77 | 100.44 | 107.26 |
| 4 requests | 61.32 | 102.76 | 198.64 |
| 8 requests | 71.74 | 114.45 | 335.93 |

\* For standard chat completion usages

\*\* Mean metrics stand for the average performance, p99 metrics stand for the worst-performant percentile

### Phi-3-mini-4k-instruct

Natively-accelerated GPU generation (**recommended**): `Ampere (SM 8.0)` or higher

Natively-supported GPU generation: `Ampere (SM 8.0)` or higher

Minimal supported GPU generation: `Volta (SM 7.0)`

**NVIDIA A10 Tensor Core GPU (Ampere architecture, SM 8.0)**

The following table shows the recommended settings and expected running times for this GPU type:

| Setting | Value |
|---|---|
| Recommended GPU utilization | 0.85 |
| Max model context length | 4,096 |
| Required memory | 8.443 GB |

**Expected running times**

The following table provides performance metrics for standard chat completion usages:

| Maximal concurrency | Mean TTFT (ms)** | P99 TTFT (ms)** | Output throughput (tokens/s) |
|---|---|---|---|
| 1 requests | 40.64 | 82.49 | 56.15 |
| 2 requests | 57.25 | 99.81 | 108.77 |
| 4 requests | 59.62 | 102.23 | 207.15 |
| 8 requests | 68.0 | 113.66 | 373.05 |

\* For standard chat completion usages

\*\* Mean metrics stand for the average performance, p99 metrics stand for the worst-performant percentile

### Phi-3.5-mini-instruct

Natively-accelerated GPU generation (**recommended**): `Ampere (SM 8.0)` or higher

Natively-supported GPU generation: `Ampere (SM 8.0)` or higher

Minimal supported GPU generation: `Volta (SM 7.0)`

**NVIDIA A10 Tensor Core GPU (Ampere architecture, SM 8.0)**

The following table shows the recommended settings and expected running times for this GPU type:

| Setting | Value |
|---|---|
| Recommended GPU utilization | 0.85 |
| Max model context length | 29,472 |
| Required memory | 8.428 GB |

**Expected running times**

The following table provides performance metrics for standard chat completion usages:

| Maximal concurrency | Mean TTFT (ms)** | P99 TTFT (ms)** | Output throughput (tokens/s) |
|---|---|---|---|
| 1 requests | 40.94 | 84.21 | 55.61 |
| 2 requests | 58.64 | 102.78 | 104.01 |
| 4 requests | 66.2 | 106.78 | 180.48 |
| 8 requests | 72.13 | 115.11 | 341.75 |

\* For standard chat completion usages

\*\* Mean metrics stand for the average performance, p99 metrics stand for the worst-performant percentile

### Phi-4-mini-instruct

Natively-accelerated GPU generation (**recommended**): `Ampere (SM 8.0)` or higher

Natively-supported GPU generation: `Ampere (SM 8.0)` or higher

Minimal supported GPU generation: `Volta (SM 7.0)`

**NVIDIA A10 Tensor Core GPU (Ampere architecture, SM 8.0)**

The following table shows the recommended settings and expected running times for this GPU type:

| Setting | Value |
|---|---|
| Recommended GPU utilization | 0.85 |
| Max model context length | 93,520 |
| Required memory | 7.806 GB |

**Expected running times**

The following table provides performance metrics for standard chat completion usages:

| Maximal concurrency | Mean TTFT (ms)** | P99 TTFT (ms)** | Output throughput (tokens/s) |
|---|---|---|---|
| 1 requests | 40.32 | 62.34 | 49.96 |
| 2 requests | 61.96 | 88.3 | 93.78 |
| 4 requests | 64.83 | 85.43 | 126.83 |
| 8 requests | 71.3 | 107.32 | 196.12 |

\* For standard chat completion usages

\*\* Mean metrics stand for the average performance, p99 metrics stand for the worst-performant percentile

### Phi-4-mini-reasoning

Natively-accelerated GPU generation (**recommended**): `Ampere (SM 8.0)` or higher

Natively-supported GPU generation: `Ampere (SM 8.0)` or higher

Minimal supported GPU generation: `Volta (SM 7.0)`

**NVIDIA A10 Tensor Core GPU (Ampere architecture, SM 8.0)**

The following table shows the recommended settings and expected running times for this GPU type:

| Setting | Value |
|---|---|
| Recommended GPU utilization | 0.85 |
| Max model context length | 93,520 |
| Required memory | 7.806 GB |

**Expected running times**

The following table provides performance metrics for standard chat completion usages:

| Maximal concurrency | Mean TTFT (ms)** | P99 TTFT (ms)** | Output throughput (tokens/s) |
|---|---|---|---|
| 1 requests | 41.53 | 62.45 | 49.92 |
| 2 requests | 64.37 | 88.6 | 91.54 |
| 4 requests | 68.26 | 94.31 | 169.38 |
| 8 requests | 90.47 | 117.99 | 244.84 |

\* For standard chat completion usages

\*\* Mean metrics stand for the average performance, p99 metrics stand for the worst-performant percentile

## Mistral AI

### Mistral-7B-Instruct-v0.2

Natively-accelerated GPU generation (**recommended**): `Ampere (SM 8.0)` or higher

Natively-supported GPU generation: `Ampere (SM 8.0)` or higher

Minimal supported GPU generation: `Volta (SM 7.0)`

**NVIDIA A10 Tensor Core GPU (Ampere architecture, SM 8.0)**

The following table shows the recommended settings and expected running times for this GPU type:

| Setting | Value |
|---|---|
| Recommended GPU utilization | 0.85 |
| Max model context length | 29,328 |
| Required memory | 15.64 GB |

**Expected running times**

The following table provides performance metrics for standard chat completion usages:

| Maximal concurrency | Mean TTFT (ms)** | P99 TTFT (ms)** | Output throughput (tokens/s) |
|---|---|---|---|
| 1 requests | 75.97 | 146.06 | 30.34 |
| 2 requests | 108.29 | 179.27 | 58.22 |
| 4 requests | 110.83 | 179.02 | 111.96 |
| 8 requests | 123.91 | 241.35 | 182.04 |

\* For standard chat completion usages

\*\* Mean metrics stand for the average performance, p99 metrics stand for the worst-performant percentile

## OpenAI

### gpt-oss-20b

Natively-accelerated GPU generation (**recommended**): `Blackwell (SM 10.0)` or higher

Natively-supported GPU generation: `Blackwell (SM 10.0)` or higher

Minimal supported GPU generation: `Volta (SM 7.0)`

**NVIDIA A10 Tensor Core GPU (Ampere architecture, SM 8.0)**

The following table shows the recommended settings and expected running times for this GPU type:

| Setting | Value |
|---|---|
| Recommended GPU utilization | 0.8 |
| Max model context length | 96,784 |
| Required memory | 14.793 GB |

**Expected running times**

The following table provides performance metrics for standard chat completion usages:

| Maximal concurrency | Mean TTFT (ms)** | P99 TTFT (ms)** | Output throughput (tokens/s) |
|---|---|---|---|
| 1 requests | 52.68 | 232.26 | 97.54 |
| 2 requests | 57.48 | 97.07 | 147.96 |
| 4 requests | 69.18 | 108.85 | 227.53 |
| 8 requests | 87.54 | 169.05 | 354.41 |

\* For standard chat completion usages

\*\* Mean metrics stand for the average performance, p99 metrics stand for the worst-performant percentile
