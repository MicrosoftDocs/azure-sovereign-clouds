---
title: Disconnected Operations Overview
description: Learn about disconnected operations for Azure Local, a deployment model that enables running fully on-premises without a connection to Azure, while maintaining a consistent Azure Local platform experience.
author: ronmiab
ms.topic: overview
ms.date: 05/06/2026
ms.author: robess
ms.service: azure
ms.subservice: sovereign-private-clouds
---

# Disconnected operations overview

This article provides an overview of disconnected operations for Azure Local.

Disconnected operations enable you to run Azure Local in environments without a connection to the Azure public cloud. This deployment model supports sovereign, classified, regulated, and edge scenarios where connectivity to Azure is restricted or unavailable.

Azure Local provides a consistent platform experience for compute, storage, networking, and lifecycle management, all running entirely on-premises. Its control plane runs locally, giving you full operational control. It offers a subset of cloud capabilities and supports a consistent operating model across connected and disconnected environments.

## What does "disconnected" mean?

In a disconnected Azure Local deployment:

- No ongoing connectivity to Azure or the internet is required.
- Core infrastructure and workloads run fully on-premises.
- Updates and servicing are performed offline or through staged workflows.
- Identity, monitoring, and access control are managed locally.
- The control plane operates locally.

Disconnected operations are commonly used in environments where data sovereignty or regulatory requirements restrict cloud connectivity.

## Supported deployment

Disconnected operations support hyperconverged Azure Local deployments that run entirely on-premises. Each deployment is associated with a single site, such as a datacenter. You can share a control plane across multiple sites, but you must provide private network connectivity between those sites.

### Hyperconverged deployments

Hyperconverged deployments in disconnected operations have the following characteristics:

- **Flexible node scale:** Supports single-node to multi-node clusters.
- **Integrated compute and storage:** Compute and storage run on the same nodes by using local disks.
- **Direct-attached storage options:** Uses locally attached storage.
- **Single-site networking:** Designed for single-site topologies by using self-contained, high-speed Ethernet without scale-out fabrics.
- **GPU support:** Supports GPU-enabled nodes for AI and compute-intensive workloads.

## Requirements for disconnected operations

To use disconnected operations, you must meet the following requirements:

- **Agreement.** Have an eligible Microsoft agreement, such as Microsoft Customer Agreement for Enterprises (MCAE).
- **Business or regulatory need.** Provide a documented requirement to operate without Azure connectivity.
- **Operational readiness.** Have the staff, processes, and partner support required to deploy and operate the environment.
- **Workload and capacity planning.** Define workloads and complete sizing before deployment.
- **Hardware.** Use supported Azure Local hardware.
- **Infrastructure.** Deploy a dedicated management cluster to host Azure Local infrastructure components.

## Workloads and services

Disconnected environments support a subset of Azure Local workloads and services, as summarized in the following table:

| Category | Workload and Services | Description |
|--|--|--|
| Core Infrastructure | Virtual Machines | Windows and Linux workloads |
| Core Infrastructure | AKS on Azure Local | Kubernetes for containerized apps |
| Data and AI | AI Document Intelligence | Extract structured data from documents |
| Data and AI | AI Language | Text analysis and NLP scenarios |
| Data and AI | AI Translator | Translation across languages |
| Data and AI | Vision Container | OCR for images and documents |
| Management and Security | Key Vault | Secrets and key management |
| Management and Security | Policy | Governance enforcement |
| Management and Security | Portal / CLI | Resource management tools |
| Management and Security | Resource Manager | Declarative deployment and RBAC |
| Workloads and Applications | Container Registry | Local container image storage |
| Workloads and Applications | Kubernetes | Container orchestration |
| Workloads and Applications | Microsoft 365 Local | Exchange, SharePoint, Skype for Business |

## Related content

- [Disconnected operations for Azure Local](/azure/azure-local/manage/disconnected-operations-overview)
- Read the [Azure Local with Disconnected Operations](https://techcommunity.microsoft.com/blog/azurearcblog/cloud-infrastructure-for-disconnected-environments-enabled-by-azure-arc/4413561) blog post.
