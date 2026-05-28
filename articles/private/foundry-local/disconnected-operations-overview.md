---
title: "Foundry Local Enabled by Azure Arc in Disconnected Environments Overview"
description: "Deploy and run AI models on Azure Local with Arc-enabled Kubernetes for secure inference in disconnected environments."
ms.service: azure
ms.subservice: sovereign-private-clouds
appliesto:
- Foundry Local on Azure Local
ms.topic: article
ms.author: cwatson
author: cwatson-cat
ms.date: 05/27/2026
ai-usage: ai-assisted
customer intent: As a platform engineer or developer, I want to understand disconnected operations in Foundry Local on Azure Local so that I can run and manage AI inference workloads disconnected on-premises.
---

# Foundry Local Enabled by Azure Arc in disconnected environments overview

Microsoft Foundry Local can be deployed on Azure Local in disconnected environments with a deployment model that is largely consistent with connected scenarios. However, several key differences apply when internet connectivity isn't available.

* **Extension availability**
Before you can install the Foundry Local Azure Arc extension, you must import the Foundry Local expansion pack into the disconnected environment.

* **Certificate management**

The `azure-cert-manager` extension isn't available in disconnected environments. Instead, you must install:

`cert-manager`
`trust-manager`

These Helm charts and container images are included in the Foundry Local expansion pack.

* **Model catalog source**
Foundry Local pulls model artifacts from the local `edgeartifacts` container registry. Model expansion packs populate this registry.

* **Telemetry**
Telemetry isn't transmitted to Microsoft. To collect diagnostics for support, use the `az k8s-extenstion troubleshoot` command.

* **Authentication**
Authentication doesn't use public Microsoft Entra ID endpoints. Instead, Foundry Local integrates with the Active Directory infrastructure configured in the disconnected Azure Local environment.

* **Authorization**

Authorization uses standard Azure RBAC roles on the Foundry extension resource:

* `Reader` is for read-only operations, such as listing and getting model catalog entries.
* `Contributor` is required for control plane write operations (for example `POST`, `PUT`, `PATCH`, `DELETE` for models and deployments) and for data plane inference operations such as `predict` and `chat/completions`.

This differs from connected deployments, which typically use roles such as Cognitive Services OpenAI User to grant access to inference endpoints.

The following diagram shows how these components work together in a disconnected environment.

:::image type="content" source="media/disconnected-operations-overview/disconnected-architecture.png" alt-text="Diagram of Foundry Local on Azure Local architecture with Arc-managed extension, inference operator and model resources in a disconnected environment, and app calls to secured inference endpoints.":::
