---
title: "Known issues for Foundry Local on Azure Local"
titleSuffix: Foundry Local on Azure Local
description: "Known issues, limitations, and workarounds for Foundry Local on Azure Local during the preview release."
ms.service: azure
ms.subservice: sovereign-private-clouds
appliesto:
- Foundry Local on Azure Local
ms.topic: troubleshooting
ms.author: cwatson
author: cwatson-cat
ms.date: 04/14/2026
ai-usage: ai-assisted
customer intent: As a platform engineer or developer, I want to know about current limitations and workarounds for Foundry Local on Azure Local so that I can plan deployments and avoid known problems.
---

# Known issues for Foundry Local on Azure Local

This article describes known limitations and workarounds for Foundry Local on Azure Local during the preview release.

[!INCLUDE [foundry-local-preview](includes/foundry-local-preview.md)]

## Known issues and workarounds

### No automatic API key rotation

**Issue:** The inference operator doesn't support automatic rotation of API keys.

**Workaround:** Delete the Kubernetes secret for the deployment. The operator recreates it automatically with new keys.

```bash
kubectl delete secret <deployment-name>-api-keys -n foundry-local-operator
```

> [!IMPORTANT]
> Deleting the secret replaces both the primary and secondary keys at once. All clients using either key lose access until you update them with the new keys. For a zero-downtime rotation approach, see [Rotate API keys](how-to-configure-tls-authentication.md#rotate-api-keys).

---

### Secrets and certificates aren't synced to other namespaces

**Problem:** API key secrets and TLS certificates aren't automatically distributed to namespaces outside of `foundry-local-operator`.

**Workaround:** Install Trust Manager by using the following required flags:

```bash
helm upgrade --install trust-manager jetstack/trust-manager \
  --namespace cert-manager \
  --set defaultPackage.enabled=false \
  --set secretTargets.enabled=true \
  --set secretTargets.authorizedSecretsAll=true
```

These flags are required for cross-namespace secret distribution to work correctly.

---

### ModelDeployment port update doesn't reconcile the NGINX sidecar

**Problem:** Updating `spec.port` on an existing ModelDeployment doesn't update the NGINX sidecar configuration. The sidecar continues using the original port, which causes inference requests to fail.

**Workaround:** Delete the ModelDeployment, correct the port value, and then reapply it:

```bash
kubectl delete -f <model-deployment>.yaml
# Edit the manifest to set the correct port value
kubectl apply -f <model-deployment>.yaml
```

---

## Related content

- [Configure TLS and authentication for Foundry Local on Azure Local](how-to-configure-tls-authentication.md)
- [What is Foundry Local on Azure Local?](what-is-foundry-local-on-azure-local.md)
- [Request deployment access](what-is-foundry-local-on-azure-local.md#request-deployment-access)
