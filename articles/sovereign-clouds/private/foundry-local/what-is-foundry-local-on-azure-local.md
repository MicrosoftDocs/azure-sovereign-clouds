---
title: "What is Foundry Local on Azure Local?"
titleSuffix: Foundry Local on Azure Local
description: "Deploy and run AI models on Azure Local with Arc-enabled Kubernetes for secure, on-premises inference."
ms.service: azure
ms.subservice: sovereign-private-clouds
appliesto:
- Foundry Local on Azure Local
ms.topic: overview
ms.author: cwatson
author: cwatson-cat
ms.date: 03/25/2026
ai-usage: ai-assisted
customer intent: As a platform engineer or developer, I want to understand Foundry Local on Azure Local so that I can run and manage AI inference workloads on-premises.
---

# What is Foundry Local on Azure Local?

Foundry Local on Azure Local brings AI inference to your Azure Local environment. Deploy and run AI models on an Arc-enabled Kubernetes cluster with Kubernetes-native operations. Keep your data processing on-premises where your data is generated.

This deployment model is designed for organizations that need local control, low-latency inference, and integration with existing Kubernetes operations on Azure Local.

[!INCLUDE [foundry-local-preview](includes/foundry-local-preview.md)]

## Key capabilities

- Run AI inference workloads on Azure Local with Kubernetes-native operations.
- Deploy and manage models through custom resources instead of manual service wiring.
- Use OpenAI-compatible REST patterns for application integration.
- Support CPU and GPU-backed deployments based on workload and hardware profile.
- Secure endpoint access using API keys and TLS-enabled ingress patterns.
- Sync model catalog metadata so teams can discover and deploy supported models consistently.

## Architecture summary

Foundry Local on Azure Local runs on an Arc-enabled Kubernetes cluster and uses an operator-based control plane for model lifecycle management. At a high level:

- The **Kubernetes inference operator** watches cluster state and reconciles model resources.
- A **Model** resource defines model metadata and availability in the cluster.
- A **ModelDeployment** resource defines runtime intent, such as scaling profile and endpoint exposure.
- The platform can synchronize model catalog metadata into the cluster for discoverability and version consistency.
- Inference traffic is exposed through internal services or ingress, protected with API key authentication and TLS.

The following diagram shows how these components work together. Your Azure Local cluster connects through Arc for management and compliance, while the Kubernetes inference operator orchestrates model deployments on Kubernetes nodes. Applications then call deployed models through API endpoints secured with API keys.

:::image type="content" source="media/foundry-local-azure-local-architecture.svg" alt-text="Architecture diagram showing Foundry Local on Azure Local components including Azure Arc, Kubernetes cluster, Inference Operator, Model Deployments, API endpoints, and integration with Azure Foundry Storage and Model Catalog." lightbox="media/foundry-local-azure-local-architecture.svg" border="false":::

For Azure platform context, see [Azure Arc-enabled Kubernetes](/azure/azure-arc/kubernetes/overview) and [What is Azure Local?](/azure/azure-local/overview).

## How it works

Foundry Local on Azure Local uses these core components:

- **Inference operator**: Kubernetes operator used by Foundry Local on Azure Local to install and manage inference components and reconcile model lifecycle changes.
- **Model and ModelDeployment CRDs**: Declarative resources used to define available models and active serving deployments.
- **Catalog sync**: Brings model catalog metadata into the cluster so supported models can be selected and deployed consistently.
- **API key authentication**: Protects inference endpoints by requiring bearer-token style API keys for requests.
- **TLS and ingress**: Secures traffic in transit and enables controlled external access through ingress.

## Prerequisites scope

To use Foundry Local on Azure Local, plan for these prerequisites at a high level:

- Azure Local environment with Kubernetes cluster capacity sized for your target models.
- Arc connection for Kubernetes management and extension-based lifecycle operations.
- Appropriate compute profile (CPU-only or GPU-enabled nodes) and validated drivers and plugins for GPU scenarios.
- Kubernetes operational access and cluster-level permissions for deploying operators and custom resources.
- Network and security posture for ingress, certificate management, and API key handling.

For implementation details, see the get-started guide in Related content.

## Supported workloads

Foundry Local on Azure Local supports AI inference workloads such as:

- **Generative AI inference**: Chat-style and text generation scenarios through OpenAI-compatible request patterns.
- **Predictive AI inference**: Non-generative model serving for classification, scoring, or other deterministic prediction tasks.
- **CPU and GPU execution**: Flexible deployment targets based on performance and cost requirements.
- **Multi-model serving patterns**: Multiple model deployments managed declaratively within the same cluster.

## When to use

Use Foundry Local on Azure Local when you need to:

- Keep inference and data processing on-premises for sovereignty or regulatory requirements.
- Reduce round-trip latency by serving models near applications and data sources.
- Standardize AI serving with Kubernetes-native operations in existing platform workflows.
- Use Azure-connected management patterns while running inference on local infrastructure.

## Related content

- [Deploy Foundry Local on Azure Local](deploy-foundry-local-on-azure-local.md)
- [Known issues for Foundry Local on Azure Local](known-issues.md)
- [Azure Arc-enabled Kubernetes overview](/azure/azure-arc/kubernetes/overview)
- [Azure Local overview](/azure/azure-local/overview)
