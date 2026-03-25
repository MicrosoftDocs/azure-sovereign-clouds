---
title: "Inference operator and model lifecycle in Foundry Local on Azure Local"
titleSuffix: Foundry Local on Azure Local
description: "Understand how the inference operator manages model lifecycle, catalog sync, and resource reconciliation in Foundry Local on Azure Local."
ms.service: azure
ms.subservice: sovereign-private-clouds
appliesto:
- Foundry Local on Azure Local
ms.topic: conceptual
ms.author: cwatson
author: cwatson-cat
ms.date: 03/25/2026
ai-usage: ai-assisted
customer intent: As a platform engineer or developer, I want to understand how the inference operator manages models in Foundry Local on Azure Local so that I can deploy and manage AI workloads effectively.
---

# Inference operator and model lifecycle in Foundry Local on Azure Local

This article explains how the inference operator works, how it manages the model lifecycle through Kubernetes custom resources, and how the model catalog integrates with your cluster. It also covers how generative and predictive model workloads differ and how to run inference against each.

[!INCLUDE [foundry-local-preview](includes/foundry-local-preview.md)]

## What the inference operator does

The inference operator is a Kubernetes operator that simplifies deploying AI models for inference. It manages the complete lifecycle of model deployments by:

- Creating Kubernetes Deployments, Services, and Ingress resources.
- Configuring CPU and GPU workloads.
- Managing API key authentication.
- Handling TLS certificates for secure communication.

## Two-resource model

The operator uses two Custom Resource Definitions (CRDs):

| Resource | Purpose | Short name |
|----------|---------|------------|
| **Model** | Defines a model available for deployment | `mdl` |
| **ModelDeployment** | Creates a running inference endpoint | `mdep` |

By separating model definition from deployment, you can:

- Reuse model definitions across multiple deployments.
- Update deployments without changing model definitions.
- Manage models and deployments independently.

For quick deployments, you can skip creating a Model resource. The ModelDeployment can reference catalog models directly, and the operator creates the Model automatically through *lazy registration*.

## How the operator reconciles resources

When you create or update a Model or ModelDeployment resource, the operator:

1. Validates the resource specification.
1. Resolves the model source (catalog or custom registry).
1. Creates child resources - Deployment, Service, and optionally Ingress.
1. Updates the resource status with endpoint information.

:::image type="content" source="media/how-inference-operator-works.svg" alt-text="Diagram showing the inference operator reconciliation flow: resource creation triggers validation, model resolution, child resource creation, and status update.":::

### ModelDeployment lifecycle states

| State | Description |
|-------|-------------|
| `Pending` | Resource created, waiting for processing. |
| `Creating` | Operator is creating child resources. |
| `Running` | All replicas are ready and serving traffic. |
| `Updating` | Applying configuration changes. |
| `Error` | Something went wrong - check the status message. |
| `Terminating` | Resource is being deleted. |

## Model catalog

The model catalog is a ConfigMap that stores metadata about available models from the Foundry catalog. It serves as a local cache: when you deploy a catalog model, the operator reads from this ConfigMap to get model details like display name, variants, and requirements.

:::image type="content" source="media/model-catalog-data-flow.svg" alt-text="Diagram showing the model catalog data flow: the catalog-sync component fetches metadata from the Azure Foundry Catalog API and stores it in the foundry-local-catalog ConfigMap in the cluster.":::

### Query the catalog

To see what models are available in your cluster, use the following methods:

#### [Bash](#tab/catalog-bash)

```bash
kubectl get cm foundry-local-catalog -n foundry-local-operator -o json \
  | jq -r '.data."catalog.json"' \
  | jq -r '["ALIAS", "DEVICE", "SIZE", "MODEL_ID"],
    (.models[] | [.alias, (.variants[0].compute | ascii_upcase),
    ((.variants[0].fileSizeBytes / 1073741824 * 100 | floor) / 100 | tostring + "GB"),
    .variants[0].id]) | @tsv' \
  | column -t
```

#### [PowerShell](#tab/catalog-powershell)

```powershell
$json    = kubectl get cm foundry-local-catalog -n foundry-local-operator -o json | ConvertFrom-Json
$catalog = $json.data.'catalog.json' | ConvertFrom-Json

$results  = @()
$results += [PSCustomObject]@{ ALIAS = "ALIAS"; DEVICE = "DEVICE"; SIZE = "SIZE"; MODEL_ID = "MODEL_ID" }

foreach ($model in $catalog.models) {
  $variant = $model.variants[0]
  $sizeGB  = [math]::Floor($variant.fileSizeBytes / 1073741824 * 100) / 100
  $results += [PSCustomObject]@{
    ALIAS    = $model.alias
    DEVICE   = $variant.compute.ToUpper()
    SIZE     = "$($sizeGB)GB"
    MODEL_ID = $variant.id
  }
}

$results | Format-Table -AutoSize
```

---

The output looks similar to the following example:

```
ALIAS                                    DEVICE  SIZE     MODEL_ID
Phi-4-generic-cpu                        CPU     8.13GB   Phi-4-generic-cpu:1
Phi-4-cuda-gpu                           GPU     8.13GB   Phi-4-cuda-gpu:1
qwen2.5-coder-0.5b-instruct-cpu          CPU     0.43GB   qwen2.5-coder-0.5b-instruct-generic-cpu:4
...
```

### Catalog ConfigMap structure

The catalog is stored in a ConfigMap with a single key (`catalog.json`). Each model entry in the `models` array contains:

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Unique model identifier |
| `alias` | string | Short name for the model |
| `displayName` | string | Human-readable name |
| `description` | string | Model description |
| `publisher` | string | Model publisher (for example, "Microsoft") |
| `license` | string | License identifier (for example, "MIT") |
| `task` | string | Model task (for example, "chat-completion") |
| `contextLength` | integer | Maximum context window size |
| `variants` | array | Hardware-specific variants |
| `supportedCompute` | array | Supported compute types (`cpu`, `gpu`) |

Each variant contains:

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Variant identifier |
| `compute` | string | Compute type (`cpu` or `gpu`) |
| `executionProvider` | string | ONNX execution provider |
| `fileSizeBytes` | integer | Model file size |

### How catalog sync works

The catalog-sync component automatically populates the catalog:

- **Initial sync**: Runs as a Helm post-install hook when you install the operator.
- **Scheduled sync**: Runs daily through a Kubernetes CronJob.

Each sync cycle:

1. Fetches model metadata from the Azure Foundry Catalog API.
1. Transforms the metadata to a concise format.
1. Creates or updates the `foundry-local-catalog` ConfigMap.

Check the last sync time:

```bash
kubectl get configmap foundry-local-catalog -n foundry-local-operator \
  -o jsonpath='{.metadata.annotations.foundry\.azure\.com/last-sync}'
```

Trigger a manual sync:

```bash
kubectl create job --from=cronjob/foundry-local-catalog-sync manual-sync \
  -n foundry-local-operator
```

### Lazy model registration

When you create a ModelDeployment that references a catalog model, the operator automatically creates a Model CR if one doesn't exist:

1. You create a ModelDeployment with `model.catalog.name: Phi-4-generic-cpu`.
1. The operator checks whether a Model CR named `phi-4-generic-cpu` exists.
1. If not, it reads the catalog ConfigMap and finds the matching model.
1. It creates a Model CR with data from the catalog.
1. The ModelDeployment proceeds with the newly created Model.

Model CRs persist after creation for reuse. To turn off lazy registration, set `catalog.lazyRegistrationEnabled: false` in the operator configuration.

## Work with Model resources

The Model resource defines a model that's available for deployment. Models can come from two sources:

| Source | Description | Use case |
|--------|-------------|---------|
| **Catalog** | Models from the Foundry catalog | Standard models (Phi, Llama, and others) |
| **Custom** | Models from your own OCI registry | Fine-tuned or proprietary models |

### Create a catalog model

```yaml
apiVersion: foundrylocal.azure.com/v1
kind: Model
metadata:
  name: phi-4
spec:
  displayName: "Phi-4"
  description: "Microsoft's Phi-4 model"
  publisher: "Microsoft"
  license: "MIT"
  source:
    type: catalog
    catalog:
      alias: Phi-4-generic-cpu
```

Apply and verify:

```bash
kubectl apply -f model.yaml
kubectl get models
kubectl describe model phi-4
```

### Create a custom (BYO) model

First, create a secret with your registry credentials:

```bash
kubectl create secret generic my-registry-credentials \
  --from-literal=username=<your-username> \
  --from-literal=password=<your-password>
```

Then create the Model resource:

```yaml
apiVersion: foundrylocal.azure.com/v1
kind: Model
metadata:
  name: my-custom-model
spec:
  displayName: "My Custom Model"
  description: "A fine-tuned model for my use case"
  publisher: "My Organization"
  license: "Proprietary"
  source:
    type: custom
    custom:
      registry: myregistry.azurecr.io
      repository: models/my-custom-model
      tag: v1.0.0
      credentials:
        secretRef:
          name: my-registry-credentials
          usernameKey: username
          passwordKey: password
```

For the full procedure to deploy a custom model and run inference against it, see [Run inference on Foundry Local on Azure Local](how-to-run-inference.md).

## Generative models

Generative AI models produce new content - like text - in response to prompts. Foundry Local on Azure Local supports generative text models for conversations, question answering, and text generation tasks. Both CPU and GPU hardware are supported.

### Use models from the Foundry catalog

You can pull and deploy models directly from the Azure AI Foundry catalog through the inference operator as described in the model catalog section above. Alternatively, you can deploy them directly from a ModelDeployment. For the full deployment procedure, see [Deploy Foundry Local on Azure Local](deploy-foundry-local-on-azure-local.md).

### Load custom models (BYO)

You can also deploy models you package yourself as Docker or OCI images - for example, custom or third-party models not in the Foundry catalog. You can deploy any model you can convert to ONNX Runtime format. For the full BYO deployment and inference procedure, see [Run inference on Foundry Local on Azure Local](how-to-run-inference.md).

### Run generative inference

Generative model endpoints follow OpenAI-compatible request patterns. The inference endpoint is `/v1/chat/completions`.

Example using a deployed `phi-3.5-gpu` model:

```bash
curl -X POST https://<your-domain>/phi-3.5-gpu/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $API_KEY" \
  -d '{
    "model": "Phi-3.5-mini-instruct-cuda-gpu:1",
    "messages": [
      {"role": "system", "content": "You are a helpful assistant."},
      {"role": "user", "content": "What is the capital/major city of France?"}
    ],
    "temperature": 0.7,
    "max_tokens": 100
  }'
```

Notes on this request:

- **URL**: If you're using an ingress controller, use the ingress IP or DNS. Without an ingress controller, use `kubectl port-forward` and set the URL to `127.0.0.1`.
- **Authorization header**: The inference operator generates API keys that you pass as a Bearer token.
- **Body**: Standard OpenAI Chat Completions payload - model, messages array, and generation parameters.
- **Response**: The model returns a JSON response where the `content` field in `choices[0].message` contains the generated text.

For more authentication header options, see [Inference API endpoints and payload reference](reference-inference-api-endpoints-payload.md).

## Predictive models

In addition to generative AI, Foundry Local on Azure Local supports predictive AI inference. This high-performance engine runs ONNX-based machine learning models. It provides a REST API for real-time inference for classification, regression, object detection, and other ML tasks.

**Key capabilities:**

- **ONNX Runtime**: Execute models in Open Neural Network Exchange (ONNX) format, which works across frameworks such as PyTorch, TensorFlow, and scikit-learn.
- **CPU and GPU execution**: Deploy on CPU nodes for cost efficiency or GPU-accelerated nodes for higher throughput.
- **Batch processing**: Process multiple inference requests with configurable batch sizes (default: 32).
- **BYO model support**: Load custom ONNX models from ORAS-compatible container registries such as Azure Container Registry, GitHub Container Registry, and Docker Hub.

> [!NOTE]
> The preview doesn't include a broad catalog of predictive models. To deploy predictive models, use BYO methods. For the full procedure, see [Run inference on Foundry Local on Azure Local](how-to-run-inference.md).

Ideally, have your model in ONNX format. It's framework-agnostic, and the ModelDeployment is built around ONNX Runtime.

### Run predictive inference

Predictive models use the `/v1/predict` endpoint. This example sends a base64-encoded image for inference:

```powershell
$base64Image = [Convert]::ToBase64String(
  [System.IO.File]::ReadAllBytes("<PATH_TO_IMAGE_FILE>")
)

$body = @{
  items = @(
    @{
      content_type = "image/jpeg"
      encoder      = "base64"
      data         = $base64Image
    }
  )
} | ConvertTo-Json -Depth 3 -Compress

Invoke-RestMethod -Uri https://<URL>/v1/predict -Method Post `
  -Body $body -ContentType "application/json" `
  -Headers @{ "X-API-Key" = $API_KEY } -SkipCertificateCheck
```

What happens in this flow:

1. Your POST request goes to `/v1/predict` with the image payload.
1. The API key in the header authenticates the request - all inference endpoints, generative and predictive, require a key.
1. NGINX validates the key and routes the request to the predictive model service.
1. The model container preprocesses the image, runs ONNX Runtime inference, and returns a structured response with the inference result.

## Related content

- [Deploy Foundry Local on Azure Local](deploy-foundry-local-on-azure-local.md)
- [Run inference on Foundry Local on Azure Local](how-to-run-inference.md)
- [ModelDeployment and operator configuration reference](reference-model-deployment-operator.md)
- [Inference API endpoints and payload reference](reference-inference-api-endpoints-payload.md)

