---
title: "Plan to deploy Foundry Local as an Azure Arc extension in a disconnected environment"
description: "Fulfill prerequisites and download the Foundry Local extension expansion pack to prepare for deployment in a disconnected environment."
ms.service: azure
ms.subservice: sovereign-private-clouds
appliesto:
- Foundry Local on Azure Local
ms.topic: how-to
ms.author: cwatson
author: cwatson-cat
ms.date: 05/28/2026
ai-usage: ai-assisted
customer intent: As a platform engineer, I want to fulfill the prerequisites and download the required package to deploy Foundry Local as an Azure Arc extension in my disconnected environment.
---

# Plan to deploy Foundry Local expansion pack in disconnected environments

## Prerequisites

Before you begin, ensure the following components are available:

* Azure Local Disconnected Operations is installed on-premises.
* An AKS Arc cluster on Azure Local, registered with Azure Arc as a `connectedClusters` resource.

    | Requirement | Minimum | Recommended |
    |---|---|---|
    | Worker node VM size | Standard_D4s_v3 (4 vCPU / 16 GiB) | Standard_D8s_v3 (8 vCPU / 32 GiB) |
    | Allocatable memory/node | >= 14 GiB | >= 28 GiB |
    | Worker node count | 1 | 2+ (HA or GPU pool separation) |
    | Logical network | Reachable from edgeartifacts ACR | - |

    The `az aksarc create` default `Standard_A4_v2` (8 GiB) isn't supported and can fail due to model-cache OOM followed by extension `--atomic` rollback.

* (Optional) GPU node pool (required only for GPU model variants such as `*-cuda-gpu` and `vLLM`):
    * NVIDIA DDA-passthrough SKU: `Standard_NC*_A2`, `Standard_NC*_L4_*`, `Standard_NC*_L40_*`, `Standard_NC*_L40S_*`, `Standard_NC*_RTX6000Pro_*`, or Tesla T4 `Standard_NK*`. AMD GPUs aren't supported.
    * NVIDIA mitigation INF is installed on the physical Azure Local node.
    * `nvidia/k8s-device-plugin:v0.11.0` should be mirrored into `edgeartifacts` container registry at the path expected by the auto-deployed DaemonSet, because `microsoft.gpu.gpuoperator` isn't registered in Autonomous.

## Download the Foundry Local Expansion Pack

Download the Foundry Local extension expansion pack from the Azure Local Disconnected Resource Provider in a connected environment.

**Package naming convention**

`azurelocal.pxp.microsoft.foundrylocal.k8sextension.<BUILD_VERSION>.zip`

Example:

`azurelocal.pxp.microsoft.foundrylocal.k8sextension.0.260520.7.zip`

## Import Expansion Pack into the Disconnected Environment

1. Transfer the expansion pack to the disconnected Azure Local environment.
1. Run the following commands on the `Azure Local Disconnected Operations` machine to install the expansion pack.

```ps1
Import-Module "<PATH_TO_ALDO_MODULES>\Azure.Local.ExpansionPack.psm1"

$expansionPackId = Start-AldoExpansionPackUpload `
    -ExpansionPackPath "<PATH_TO_EXPANSION_PACK>"

$result = Start-AldoExpansionPackInstallation `
    -ExpansionPackId $expansionPackId `
    -Wait
```

When installation finishes:

* The process imports container images into the `edgeartifacts` registry.
* Model artifacts are published to the registry.
* The Foundry Local Azure Arc extension becomes available for installation.

### Verify Installation

Run the following command on the `Azure Local Disconnected Operations` machine and confirm your expansion pack is in `Installed` state.

```ps1
Get-ApplianceExpansionPackDetails
```
