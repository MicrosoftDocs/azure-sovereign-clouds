---
title: "Deploy Foundry Local on Azure Local"
titleSuffix: Foundry Local on Azure Local
description: "Install cert-manager, trust-manager, and the inference operator, then deploy your first AI model on an Arc-enabled Kubernetes cluster."
ms.service: azure
ms.subservice: sovereign-private-clouds
appliesto:
- Foundry Local on Azure Local
ms.topic: how-to
ms.author: cwatson
author: cwatson-cat
ms.date: 03/25/2026
ai-usage: ai-assisted
customer intent: As a platform engineer, I want to deploy Foundry Local on Azure Local so that I can run AI inference workloads on my on-premises Kubernetes cluster.
---

# Deploy Foundry Local on Azure Local

This article shows you how to set up Foundry Local on Azure Local. You install cert-manager, trust-manager, and the inference operator, then deploy your first AI model from the Foundry catalog.

[!INCLUDE [foundry-local-preview](includes/foundry-local-preview.md)]

## Prerequisites

Before you begin, make sure you have:

- A Kubernetes cluster (version 1.29 or later) connected to Azure Arc. For more information, see [Azure Arc-enabled Kubernetes](/azure/azure-arc/kubernetes/overview).
- [kubectl](https://kubernetes.io/docs/tasks/tools/) installed and configured for your cluster.
- [Helm](https://helm.sh/docs/intro/install/) installed.
- For external endpoints: an NGINX ingress controller, such as [NGINX-Ingress](https://docs.nginx.com/nginx-ingress-controller/).

> [!IMPORTANT]
> [Ingress-NGINX](https://github.com/kubernetes/ingress-nginx) is deprecated by March 2026. Microsoft currently supports NGINX annotations and plans to add more in the future. The solution is tested with AKS's managed NGINX ingress controller.

### GPU prerequisites

If you plan to run GPU workloads, also make sure:

- NVIDIA GPU nodes are available in your cluster with CUDA drivers installed on the nodes.
- The Kubernetes device plugin for NVIDIA is configured so the cluster can schedule GPU workloads.

For more information, see [NVIDIA GPU Operator](https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/latest/getting-started.html).

## Supported configurations

Foundry Local on Azure Local is validated on following configurations:

- Arc-connected AKS clusters without GPU nodes - for CPU models.
- Arc-connected AKS clusters with H100 GPU nodes.
- Bare-metal hardware with AKS cluster enabled by Arc on Azure Local with L4 GPU nodes.

## Step 1: Install cert-manager and trust-manager

Foundry Local on Azure Local requires cert-manager and trust-manager for automated certificate management. Install cert-manager first, and wait for its pods to be ready before you install trust-manager.

> [!IMPORTANT]
> - Don't install trust-manager until cert-manager pods are running.
> - The validations use Cert-Manager v1.19.2 and Trust-Manager v0.20.3.
> - For trust-manager, set `--set secretTargets.enabled=true` and `--set secretTargets.authorizedSecretsAll=true`.

#### [Bash](#tab/install-bash)

```bash
helm repo add jetstack https://charts.jetstack.io
helm repo update

helm upgrade --install cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --create-namespace \
  --version v1.19.2 \
  --set crds.enabled=true \
  --set crds.keep=true \
  --set image.tag=v1.19.2 \
  --set webhook.image.tag=v1.19.2 \
  --set cainjector.image.tag=v1.19.2 \
  --set acmesolver.image.tag=v1.19.2 \
  --set startupapicheck.image.tag=v1.19.2 \
  --wait

helm upgrade --install trust-manager jetstack/trust-manager \
  --namespace cert-manager \
  --version v0.20.3 \
  --set image.tag=v0.20.3 \
  --set defaultPackage.enabled=false \
  --set secretTargets.enabled=true \
  --set secretTargets.authorizedSecretsAll=true \
  --wait
```

#### [PowerShell](#tab/install-powershell)

```powershell
helm repo add jetstack https://charts.jetstack.io
helm repo update

helm upgrade --install cert-manager jetstack/cert-manager `
  --namespace cert-manager `
  --create-namespace `
  --version v1.19.2 `
  --set crds.enabled=true `
  --set crds.keep=true `
  --set image.tag=v1.19.2 `
  --set webhook.image.tag=v1.19.2 `
  --set cainjector.image.tag=v1.19.2 `
  --set acmesolver.image.tag=v1.19.2 `
  --set startupapicheck.image.tag=v1.19.2 `
  --wait

helm upgrade --install trust-manager jetstack/trust-manager `
  --namespace cert-manager `
  --version v0.20.3 `
  --set image.tag=v0.20.3 `
  --set defaultPackage.enabled=false `
  --set secretTargets.enabled=true `
  --set secretTargets.authorizedSecretsAll=true `
  --wait
```

---

## Step 2: Install the inference operator

#### [Bash](#tab/operator-bash)

```bash
helm upgrade --install inference-operator \
  oci://mcr.microsoft.com/foundrylocalonazurelocal/helmcharts/helm/inferenceoperator \
  --version 0.0.1-prp.5 \
  -n foundry-local-operator \
  --create-namespace
```

#### [PowerShell](#tab/operator-powershell)

```powershell
helm upgrade --install inference-operator `
  oci://mcr.microsoft.com/foundrylocalonazurelocal/helmcharts/helm/inferenceoperator `
  --version 0.0.1-prp.5 `
  -n foundry-local-operator `
  --create-namespace
```

---

## Step 3: Verify the operator

#### [Bash](#tab/verify-bash)

```bash
kubectl get pods -n foundry-local-operator
kubectl get crd | grep foundry
```

#### [PowerShell](#tab/verify-powershell)

```powershell
kubectl get pods -n foundry-local-operator
kubectl get crd | Select-String -Pattern "foundry"
```

---

Wait until all pods show a `Running` status before you proceed.

## Step 4: Deploy a model

This step deploys a model from the Foundry catalog. The examples use Phi-4, but you can deploy any catalog model. To use a different model, query the catalog first to get available model IDs, then update these fields in the YAML:

- `metadata.name`
- `spec.displayName`
- `spec.model.catalog.name` (use the MODEL_ID column value)
- `spec.model.catalog.version` (use the MODEL_ID column value)
- `spec.workloadType` — set to `predictive` if needed
- `spec.compute` — set to `gpu` if needed

For information about querying the catalog, see [Inference operator and model lifecycle](concept-inference-operator.md).

### [CPU](#tab/deploy-cpu)

Create a file named `modeldeployment-phi-4-cpu.yaml`:

```yaml
apiVersion: foundrylocal.azure.com/v1
kind: ModelDeployment
metadata:
  name: phi-4-cpu
  namespace: foundry-local-operator
  labels:
    app.kubernetes.io/name: phi-4-cpu
    app.kubernetes.io/component: inference
    foundry.azure.com/hardware: cpu
spec:
  displayName: "Phi-4 CPU"
  model:
    catalog:
      name: Phi-4-generic-cpu
      version: "1"
  workloadType: generative
  compute: cpu
  replicas: 1
  resources:
    requests:
      cpu: "2"
      memory: "8Gi"
    limits:
      cpu: "4"
      memory: "24Gi"
  # Required only if you use an ingress controller
  endpoint:
    enabled: true
    host: <YOUR_INGRESS_ADDRESS>
```

Apply the manifest:

```bash
kubectl apply -f modeldeployment-phi-4-cpu.yaml
```

### [GPU](#tab/deploy-gpu)

Create a file named `modeldeployment-phi-4-gpu.yaml`:

```yaml
apiVersion: foundrylocal.azure.com/v1
kind: ModelDeployment
metadata:
  name: phi-4-gpu
  namespace: foundry-local-operator
  labels:
    app.kubernetes.io/name: phi-4-gpu
    app.kubernetes.io/component: inference
    foundry.azure.com/hardware: gpu
spec:
  displayName: "Phi-4 GPU"
  model:
    catalog:
      name: Phi-4-cuda-gpu
      version: "1"
  workloadType: generative
  compute: gpu
  replicas: 1
  resources:
    requests:
      cpu: "4"
      memory: "32Gi"
    limits:
      cpu: "8"
      memory: "64Gi"
      gpu: 1
  # Required only if you use an ingress controller
  endpoint:
    enabled: true
    host: <YOUR_INGRESS_ADDRESS>
  nodeSelector:
    foundry/workload: gpu
```

Apply the manifest:

```bash
kubectl apply -f modeldeployment-phi-4-gpu.yaml
```

---

## Step 5: Verify the deployment

Check the deployment status and, if you configured an ingress controller, the ingress resource:

```bash
kubectl get modeldeployments -A

# If you use an ingress controller
kubectl get ingress -A
```

Wait for **State** to show `Running` and **Ready** to show `true`. The model downloads from the internet during this step, so it might take several minutes depending on your connection and model size.

## Get the endpoint URL

After the deployment is running, get the endpoint URL.

### [With ingress controller](#tab/endpoint-ingress)

```bash
kubectl get mdep phi-4-cpu -n foundry-local-operator -o jsonpath='{.spec.endpoint.host}'
```

Access the model at `https://<YOUR_INGRESS_ADDRESS>/phi-4-cpu/`.

### [Without ingress controller](#tab/endpoint-no-ingress)

```bash
kubectl get svc -n foundry-local-operator \
  -o jsonpath='{range .items[*]}{.metadata.name}.{.metadata.namespace}.svc.cluster.local{"\n"}{end}'
```

Add port `5000` to the address. For example: `phi-4-cpu.foundry-local-operator.svc.cluster.local:5000`

You can access the endpoint within the cluster at that URL.

---

## Troubleshoot deployment issues

### Common errors

| Issue | Cause | Resolution |
|-------|-------|------------|
| ModelDeployment stuck in `Pending` | Model name not found in catalog | Verify the catalog model name is correct. |
| ModelDeployment stuck in `Creating` | Image pull error | Check image pull secrets and registry access. |
| GPU validation fails | Missing GPU limit | Set `resources.limits.gpu` when `compute: gpu`. |
| Ingress not created | Missing host value | Set `endpoint.host` when `endpoint.enabled: true`. |
| TLS not working | Missing secret | Create a TLS secret with `tls.crt` and `tls.key`. |
| Authentication error | Invalid API key | Retrieve the correct key from the deployment secret. |
| Model not found in catalog | Catalog not synced | Trigger a manual catalog sync. |

### Debug commands

Check ModelDeployment status and events:

```bash
kubectl describe mdep <name>
```

Check operator logs:

```bash
kubectl logs -f deployment/inference-operator -n foundry-local-operator
```

Check pod status:

```bash
kubectl get pods -l app.kubernetes.io/managed-by=inference-operator
kubectl describe pod <pod-name>
kubectl logs <pod-name>
```

List all resources created by a deployment:

```bash
kubectl get deploy,svc,ing -l foundry.azure.com/deployment=<name>
```

Check the catalog ConfigMap:

```bash
kubectl get configmap foundry-local-catalog -n foundry-local-operator -o yaml
```

Verify a Model CR exists:

```bash
kubectl get models
kubectl describe model <name>
```

## Related content

- [Run inference on Foundry Local on Azure Local](how-to-run-inference.md)
- [Configure TLS and authentication for Foundry Local on Azure Local](how-to-configure-tls-authentication.md)
- [Inference operator and model lifecycle](concept-inference-operator.md)
- [Known issues for Foundry Local on Azure Local](known-issues.md)
