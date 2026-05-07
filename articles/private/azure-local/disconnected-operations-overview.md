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

Use disconnected operations when data sovereignty or regulatory constraints prevent cloud connectivity.

In a disconnected Azure Local deployment:

- Connectivity to Azure or the internet isn't required for ongoing operations.
- Core infrastructure and workloads run fully on-premises.
- You perform updates and servicing offline or through staged workflows.
- You manage identity, monitoring, and access control locally.
- The control plane operates locally.

## Supported deployment

Disconnected operations support hyperconverged Azure Local deployments that run entirely on-premises. Each deployment is associated with a single site, such as a datacenter. You can share a control plane across multiple sites, but you must provide private network connectivity between those sites.

### Hyperconverged deployments

Key characteristics:

- **Node scale:** Supports single-node to multi-node clusters.
- **Compute and storage:** Run on the same nodes using local disks.
- **Storage options:** Use direct-attached local storage.
- **Networking:** Designed for single-site topologies using self-contained, high-speed Ethernet without scale-out fabrics.
- **Accelerators:** Supports GPU-enabled nodes for AI and compute-intensive workloads.

## Requirements for disconnected operations

Use disconnected operations if your organization must operate without cloud connectivity.

To use disconnected operations, you must meet the following requirements:

- **Agreement.** Have an eligible Microsoft agreement, such as Microsoft Customer Agreement for Enterprises (MCAE).
- **Business or regulatory need.** Provide a documented requirement to operate without Azure connectivity.
- **Operational readiness.** Have the staff, processes, and partner support required to deploy and operate the environment.
- **Workload and capacity planning.** Define workloads and complete sizing before deployment.
- **Hardware.** Use supported Azure Local hardware.
- **Infrastructure.** Deploy a dedicated management cluster to host Azure Local infrastructure components.

## Workloads and services

Disconnected environments support a subset of Azure Local workloads and services.

### Core infrastructure workloads

- **Virtual Machines:** Windows and Linux workloads
- **AKS on Azure Local:** Kubernetes for containerized apps

### Management and security

- **Key Vault:** Secrets and key management
- **Policy:** Governance enforcement
- **Portal / CLI:** Resource management tools
- **Resource Manager:** Declarative deployment and RBAC

### Workloads and applications

- **Container Registry:** Local container image storage
- **Kubernetes:** Container orchestration
- **Microsoft 365 Local:** Exchange, SharePoint, Skype for Business

### Data and AI

- **AI Document Intelligence:** Extract structured data from documents
- **AI Language:** Text analysis and NLP scenarios
- **AI Translator:** Translation across languages
- **Vision Container:** OCR for images and documents

## Related content

- [Disconnected operations for Azure Local](/azure/azure-local/manage/disconnected-operations-overview)
- Blog post on [Azure Local with Disconnected Operations](https://techcommunity.microsoft.com/blog/azurearcblog/cloud-infrastructure-for-disconnected-environments-enabled-by-azure-arc/4413561).
