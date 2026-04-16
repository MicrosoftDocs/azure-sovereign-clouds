---
title: What's Sovereign Private Cloud?  
description: Learn about Sovereign Private Cloud and how it runs on Azure Local. Understand other features and how they support sovereignty on Azure Local.
author: ronmiab
ms.author: robess
ms.reviewer: robess
ms.date: 04/15/2026
ms.topic: overview
ms.subservice: sovereign-private-clouds
---

# What's Sovereign Private Cloud

The Sovereign Private Cloud is a portfolio of Microsoft solutions designed to help organizations run cloud services in **sovereign, regulated, and disconnected environments**. Sovereign Private Cloud provides a consistent Microsoft cloud experience while allowing customers to retain full control over infrastructure, data residency, and operations.

Sovereign Private Cloud consists of three solution areas that run on Azure Local:

:::image type="content" source="../media/overview/solution-areas.png" alt-text="Diagram of the Sovereign Private Cloud solution areas that run on Azure Local." lightbox="../media/overview/solution-areas.png":::

## Azure Local

Azure Local is the foundation of the Sovereign Private Cloud. Azure Local provides the core infrastructure layer - compute, storage, networking, and lifecycle management - on which sovereign workloads run. All Sovereign Private Cloud solutions are built on and depend on Azure Local to deliver Azure-consistent services in customer-managed environments.

## Foundry Local on Azure Local

Foundry Local on Azure Local enables you to bring AI closer to your data by deploying and running AI models entirely within your Azure Local environment. Foundry Local supports scenarios where you need AI sovereignty, low-latency inference, and control over where your data is processed. It integrates with Arc-enabled Kubernetes so you can operationalize AI using familiar Kubernetes-native workflows while keeping AI workloads on-premises.

Foundry Local on Azure Local also supports a **Model-as-a-Service (MaaS)** approach, enabling you to deploy, manage, and consume AI models locally without building and operating the full model lifecycle yourself.

## Microsoft 365 Local

Microsoft 365 Local enables you to run Exchange Server, SharePoint Server, and Skype for Business Server on Azure Local infrastructure that you own and manage. You gain enhanced control over data residency, access, and compliance, helping you meet your sovereignty requirements.

If you need productivity tools in a private cloud environment, Microsoft 365 Local provides an Azure-consistent management experience with a unified control plane. It simplifies deployment and streamlines updates for easy infrastructure management, supporting both hybrid and fully disconnected deployments.

## Related content

[Azure Local for Azure Sovereign Private Clouds](sovereign-private-cloud.md) <!--once published on Microsoft Learn-->

[Foundry Local for Azure Sovereign Private Clouds documentation](../foundry-local/what-is-foundry-local-on-azure-local.md)

[Microsoft 365 Local on Azure Local Infrastructure](sovereign-private-cloud.md) <!--shift from Azure Local TOC to SPC TOC-->

[Sovereign design and implementation considerations](sovereign-private-cloud.md)
