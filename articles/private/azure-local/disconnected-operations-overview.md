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

Azure Local provides a consistent platform experience for compute, storage, networking, and lifecycle management, all running entirely on-premises. Disconnected operations give you full operational control by moving the control plane into your environment, where your organization operates it. It provides a subset of cloud capabilities and enables a consistent cloud operating model that spans both connected and fully disconnected environments.

## What does "disconnected" mean?

In a disconnected Azure Local deployment:

- The system doesn't require ongoing connectivity to Azure or the internet.
- Core infrastructure and workloads continue running entirely on‑premises.
- You perform updates, servicing, and onboarding by using offline or staged workflows.
- You handle identity, monitoring, and access control locally by using supported on‑premises integrations.
- The control plane runs locally and is operated and governed on-premises.

Use disconnected operations in environments where data sovereignty or regulatory requirements restrict cloud connectivity.

## Supported deployment

Disconnected operations support hyperconverged Azure Local deployments designed to run fully on-premises without reliance on Azure or internet connectivity. Each Azure Local deployment is tied to a single site, like a datacenter. You can deploy each Azure Local instance with additional resiliency or availability within the datacenter, for example by using rack aware clusters. You can share the disconnected operations control plane across sites, providing a flexible solution for a multi-site private cloud and ensuring a high level of isolation. When you share a disconnected operations control plane across multiple sites, you're responsible for providing any required private network connectivity between sites. Cross‑site connectivity is customer‑designed and depends on individual network and security requirements.

### Hyperconverged deployments

Combine compute, storage, and networking on the same nodes, providing a compact and efficient platform for disconnected environments.

- **Node scale:** Single node systems through multi node clusters (within supported limits for disconnected operations)
- **Compute and storage:** Compute, storage, and networking run on the same physical nodes, using local disks for storage.
- **Storage options:** Use local, direct attached storage to ensure full operation without external dependencies.
- **Networking considerations:**
    - Designed for single site networking topologies.
    - Use local, high speed Ethernet for management, storage, and workload traffic.
    - Networking is typically self contained, without multi rack or scale out fabrics.
- **Accelerators:**
    - Support for GPU enabled nodes in supported configurations to run AI, graphics, and compute intensive workloads locally.

## Eligibility criteria

Disconnected operations are intended for organizations with a validated requirement to operate Azure Local without connectivity to the public cloud. At a high level, eligibility includes:

- An eligible Microsoft agreement, such as a Microsoft Customer Agreement for Enterprises (MCA E).
- A documented business or regulatory requirement to operate without Azure connectivity (for example, sovereign, classified, or highly regulated environments).
- Operational readiness, including staff, processes, and partner support to deploy and run a disconnected environment.
- Pre planned workloads and capacity, with sizing completed before procurement.
- Supported, customer owned Azure Local hardware designed for disconnected operations.
- A dedicated management cluster to host required Azure Local infrastructure components.

This summary is intended to help you assess fit at a glance. For complete, eligibility requirements and onboarding details, see [Eligibility criteria for disconnected operations](/azure/azure-local/manage/disconnected-operations-overview#eligibility-criteria).

## Workloads and services

Disconnected environments support a subset of Azure Local workloads and services, as summarized in the following table. These capabilities are designed to run fully on-premises while maintaining consistency with Azure architecture and tooling.

| Category | Workloads and Services | Description |
|--|--|--|
| Core Infrastructure | Azure Local virtual machines (VMs)  | Run Windows and Linux VMs for line‑of‑business applications, infrastructure services, and legacy workloads. |
| Core Infrastructure | AKS on Azure Local (where applicable) | Run containerized applications locally using Kubernetes, with configurations tailored for disconnected environments. |
| Data and AI | AI Document Intelligence | Extract text, structure, and key information from documents to automate processing and analysis of unstructured content. |
| Data and AI | AI Language | Analyze and process text to enable scenarios such as summarization, classification, sentiment analysis, and question answering. |
| Data and AI | AI Translator | Translate text and documents across languages to support multilingual applications and global operation. |
| Data and AI | Vision Container | Extract printed and handwritten text from images and documents with support for JPEG, PNG, BMP, PDF, and TIFF file formats. |
| Management and Security | Key Vault | Securely store and manage secrets, keys and certificates used by applications and services. |
| Management and Security | Policy | Define and enforce governance rules to help ensure resources are deployed and operated in a consistent and compliant manner. |
| Management and Security | Portal CLI | Manage and operate resources by using a graphical portal or command-line tools, supporting both interactive and automated workflows. |
| Management and Security | Resource Manager | Provide a consistent control plane for deploying, managing, and organizing Azure Local resources using declarative templates, policies, and role-based access control. |
| Workloads and Applications | Container Registry | Store and manage container images locally for disconnected Kubernetes and application workflows.  |
| Workloads and Applications | Kubernetes | Orchestrate and manage containerized workloads using Kubernetes, providing consistent deployment and scaling across environments. |
| Workloads and Applications | Microsoft 365 Local | Run Exchange Server, SharePoint Server, and Skype for Business Server on Azure Local infrastructure. |

## Related content

- [Disconnected operations for Azure Local](/azure/azure-local/manage/disconnected-operations-overview)
- Read the [Azure Local with Disconnected Operations](https://techcommunity.microsoft.com/blog/azurearcblog/cloud-infrastructure-for-disconnected-environments-enabled-by-azure-arc/4413561) blog post.