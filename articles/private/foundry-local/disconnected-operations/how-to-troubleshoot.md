---
title: "Troubleshoot for Foundry Local Azure Arc Extension Deployment in Disconnected Environments"
description: "Troubleshoot problems with your Foundry Local enabled by Azure Arc deployment in a disconnected environment, including expansion pack installation, extension installation, model syncing, authentication and authorization, and GPU deployments."
ms.service: azure
ms.subservice: sovereign-private-clouds
appliesto:
- Foundry Local on Azure Local
ms.topic: how-to
ms.author: cwatson
author: cwatson-cat
ms.date: 05/27/2026
ai-usage: ai-assisted
customer intent: As a platform engineer, I want to find and fix issues with my Foundry Local deployment in a disconnected environment.
---

# Troubleshoot Foundry Local Azure Arc extension deployment in disconnected environments

## Expansion pack installation times out or stays in noninstalled state

If expansion pack installation doesn't complete, remove the failed pack and reinstall it.

```ps1
Get-ApplianceExpansionPackDetails

Remove-AldoExpansionPack -ExpansionPackId <EXPANSION_PACK_ID>

# Re-run upload + installation
$expansionPackId = Start-AldoExpansionPackUpload -ExpansionPackPath "<PATH_TO_EXPANSION_PACK>"
$result = Start-AldoExpansionPackInstallation -ExpansionPackId $expansionPackId -Wait
```

## Foundry extension installation doesn't complete

Validate that prerequisites are installed and healthy, especially NGINX ingress (if used), cert-manager, and trust-manager.

```ps1
helm list -A
kubectl get pods -n cert-manager
kubectl get pods -n ingress-nginx
kubectl get pods -n foundry-local-operator
```

If cert-manager, trust-manager, or ingress-nginx releases aren't `deployed` or their pods aren't `Running`, fix those issues first and retry extension installation.

## Collect logs for Microsoft support

Run:

```ps1
az k8s-extension troubleshoot --name foundry --namespace-list "foundry-local-operator"
```

This command collects diagnostic logs for support.

## Extension install fails with OOM or atomic rollback

If installation fails and you're using default AKS Arc worker size (`Standard_A4_v2`), recreate the cluster with at least `Standard_D4s_v3` (recommended `Standard_D8s_v3`).

```ps1
kubectl describe nodes | Select-String "Allocatable:" -Context 0,5
```

## Models don't appear after sync

Check that the model expansion pack is installed, and then run sync again.

```ps1
Get-ApplianceExpansionPackDetails

Invoke-RestMethod `
  -Uri "$baseUrl/api/v1/models/sync" `
  -Headers $headers `
  -Method POST

Invoke-RestMethod `
  -Uri "$baseUrl/api/v1/models" `
  -Headers $headers `
  -Method GET
```

## 401 or 403 errors when calling APIs

Confirm the token audience and RBAC role assignment on the Foundry extension scope.

```ps1
az account get-access-token --resource "$appId" --query accessToken -o tsv

az role assignment list `
  --assignee-object-id "<USER_OR_MI_OBJECT_ID>" `
  --scope "/subscriptions/<SUB_ID>/resourceGroups/<RG>/providers/Microsoft.Kubernetes/connectedClusters/<CLUSTER>/providers/Microsoft.KubernetesConfiguration/extensions/foundry" `
  -o table
```

Use `Reader` for read-only calls. Use `Contributor` for control plane write operations and data plane inference operations.

## GPU deployments stay pending with insufficient GPU

If GPU workloads stay `Pending` with `Insufficient nvidia.com/gpu`, verify the device-plugin image is mirrored and running.

```ps1
kubectl get pods -n kube-system -l name=nvidia-device-plugin-ds -o wide
kubectl get nodes -o custom-columns="NAME:.metadata.name,GPU-CAP:.status.capacity.nvidia\.com/gpu"
```

In disconnected Autonomous environments, ensure `nvidia/k8s-device-plugin:v0.11.0` exists in edgeartifacts at the path expected by the auto-deployed DaemonSet.
