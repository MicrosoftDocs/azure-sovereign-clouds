---
title: "Configure namespaces for model deployments"
titleSuffix: Foundry Local on Azure Local
description: "Configure additional namespaces for model deployments in Foundry Local on Azure Local by setting the watch.namespaces parameter."
ms.service: azure
ms.subservice: sovereign-private-clouds
appliesto:
- Foundry Local enabled by Azure Arc
ms.topic: how-to
ms.author: cwatson
author: cwatson-cat
ms.date: 04/28/2026
ai-usage: ai-assisted
customer intent: As a platform engineer, I want to configure additional namespaces for model deployments so that I can organize workloads across multiple Kubernetes namespaces.
---

# Configure namespaces for model deployments

By default, the Foundry on Arc Inference extension monitors only the `foundry-local-operator` namespace, along with its own release namespace. To deploy and manage models in additional namespaces, you must explicitly specify them using the `watch.namespaces` configuration during extension installation or update.

[!INCLUDE [foundry-local-preview](includes/foundry-local-preview.md)]

## Example configuration

```yaml
watch:
  namespaces:
    - "foundry-local-operator"
    - "foundry-local-workloads"
```

If a model deployment is created in a namespace that isn't listed under `watch.namespaces`, the operator doesn't have the required cluster-scoped RBAC permissions for that namespace. As a result, the model deployment fails during reconciliation due to missing permissions.

> [!IMPORTANT]
> Plan your namespace strategy carefully before installation. Changes to this configuration require an extension update to take effect, as RBAC permissions are provisioned at install/update time.

## Related content

- [Deploy Foundry Local as an Arc extension](deploy-foundry-local-arc-extension.md)
- [Get started with Foundry Local on Azure Local](getting-started.md)
