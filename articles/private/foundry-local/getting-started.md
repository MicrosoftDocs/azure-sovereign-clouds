---
title: "Get started with Foundry Local on Azure Local"
titleSuffix: Foundry Local on Azure Local
description: "Walk through your first steps with Foundry Local — from deploying the platform to running inference against your first model."
ms.service: azure
ms.subservice: sovereign-private-clouds
appliesto:
- Foundry Local on Azure Local
ms.topic: get-started
ms.author: cwatson
author: cwatson-cat
ms.date: 04/28/2026
ai-usage: ai-assisted
customer intent: As a platform engineer or developer, I want to get started with Foundry Local on Azure Local so that I can deploy and run inference against AI models on my on-premises cluster.
---

# Get started with Foundry Local on Azure Local

This guide walks you through your first steps with Foundry Local — from deploying the platform to running inference against your first model.

[!INCLUDE [foundry-local-preview](includes/foundry-local-preview.md)]

## Request deployment access

Foundry Local on Azure Local deployment is currently available by request during preview. To get started, submit the access request form: [Request preview deployment access](https://aka.ms/FoundryLocalAzure_PreviewRequest). After your request is reviewed, you'll receive guidance on next steps for deployment.

## Deployment

Before you can start working with models, Foundry Local must be deployed to your Kubernetes cluster. There are two supported installation methods:

- **Helm chart** — For direct Kubernetes deployments. See [Deploy Foundry Local on Azure Local](deploy-foundry-local-on-azure-local.md).
- **Arc extension** — For Azure Arc-enabled Kubernetes clusters. See [Deploy Foundry Local as an Arc extension](deploy-foundry-local-arc-extension.md).

If you want to deploy models in namespaces other than the default, see [Configure namespaces for model deployments](how-to-configure-namespaces.md).

## Inference quick start

Once Foundry Local is deployed and you complete authentication, you can begin listing, deploying, and interacting with models. Foundry Local supports two approaches:

- **kubectl** — Work directly with Kubernetes custom resources (ModelDeployment CRDs).
- **Foundry Local REST API** — Use HTTP endpoints exposed by the inference operator.

The sections below provide a command-by-command reference for both approaches.

### Using kubectl

#### List available models

View the full model catalog to see which models are available for deployment:

```bash
kubectl get configmap foundry-local-catalog -n foundry-local-operator -o jsonpath="{.data['catalog\.json']}"
```

For a table-style catalog:

```powershell
kubectl get configmap foundry-local-catalog -n foundry-local-operator -o jsonpath="{.data['catalog\.json']}" | ConvertFrom-Json | Select-Object -ExpandProperty models | Format-Table alias, displayName, task, framework
```

#### Deploy a model

Create a YAML file (for example, `model-deployment.yaml`) with a ModelDeployment resource. Replace the placeholder values with the model name from the catalog and your desired configuration:

```yaml
apiVersion: foundrylocal.azure.com/v1
kind: ModelDeployment
metadata:
  name: <deployment-name>
  namespace: foundry-local-operator
spec:
  model:
    catalog:
      name: <model-name-from-catalog>
      version: "latest"
  compute: gpu              # or cpu
  runtime: vllm             # or onnx-genai
  workloadType: generative
  replicas: 1
  resources:
    requests:
      cpu: "2"
      memory: "32Gi"
    limits:
      cpu: "4"
      memory: "64Gi"
      gpu: 1
```

Apply the manifest to deploy the model:

```bash
kubectl apply -f model-deployment.yaml
```

#### Check deployment status

Check whether a specific model deployment is ready:

```bash
kubectl get modeldeployment <deployment-name> -n foundry-local-operator
```

For detailed status information including events and conditions:

```bash
kubectl describe modeldeployment <deployment-name> -n foundry-local-operator
```

To list all deployed models across all namespaces:

```bash
kubectl get modeldeployment -A
```

#### Send an inference request

Once the model's status shows **Running**, set up port forwarding to the model's service:

```bash
kubectl port-forward svc/<deployment-name> -n foundry-local-operator 5000:5000
```

Retrieve the model's API key (the value is Base64-encoded):

```bash
kubectl get secret <deployment-name>-api-keys -n foundry-local-operator -o jsonpath="{.data.primary-key}"
```

Decode the key, then open a new terminal and send a chat completion request:

```bash
curl -k -X POST https://localhost:5000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "api-key: <your-decoded-api-key>" \
  -d '{"model":"<model-name>","messages":[{"role":"user","content":"Hello, what can you do?"}]}'
```

### Using the Foundry Local REST API

The Foundry Local API provides REST endpoints for managing models. Start by setting up port forwarding to the API service:

```bash
kubectl port-forward -n foundry-local-operator svc/inference-operator-api 8080:8080
```

In a new terminal, obtain an access token for API authentication:

```bash
token=$(az account get-access-token --resource "<client-id>" --query accessToken -o tsv)
```

#### List available models

```bash
curl -k -s https://localhost:8080/api/v1/models -H "Authorization: Bearer $token"
```

Or in a table format:

```powershell
$response = curl -k -s https://localhost:8080/api/v1/models -H "Authorization: Bearer $token" | ConvertFrom-Json
$response.models | Format-Table alias, source, framework, @{L='compute';E={$_.supportedCompute -join ','}} -AutoSize
```

#### Deploy a model

```bash
curl -k -X POST https://localhost:8080/api/v1/namespaces/foundry-local-operator/deployments \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $token" \
  -d '{"name":"<deployment-name>","spec":{"model":{"catalog":{"name":"<model-name>","version":"latest"}},"workloadType":"generative","compute":"cpu","runtime":"onnx-genai","replicas":1,"authentication":{"enabled":true}}}'
```

#### Check deployment status

```bash
curl -k -s https://localhost:8080/api/v1/namespaces/foundry-local-operator/deployments/<deployment-name> \
  -H "Authorization: Bearer $token"
```

#### Send an inference request

Set up port forwarding to the model's service and retrieve the API key (using the same kubectl commands shown in the kubectl section), then send a chat completion request:

```bash
curl -k -X POST https://localhost:5000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "api-key: <your-decoded-api-key>" \
  -d '{"model":"<model-name>","messages":[{"role":"user","content":"Hello, what can you do?"}],"max_tokens":256}'
```

## Related content

- [Deploy Foundry Local on Azure Local](deploy-foundry-local-on-azure-local.md)
- [Deploy Foundry Local as an Arc extension](deploy-foundry-local-arc-extension.md)
- [Configure authentication for Foundry Local enabled by Arc](how-to-configure-authentication.md)
- [Run inference on Foundry Local on Azure Local](how-to-run-inference.md)
