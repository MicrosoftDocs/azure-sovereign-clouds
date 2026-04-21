---
title: "Inference operator and model lifecycle in Foundry Local on Azure Local"
description: "Understand how the inference operator manages model lifecycle, custom resources, and resource reconciliation in Foundry Local on Azure Local."
ms.service: azure
ms.subservice: sovereign-private-clouds
appliesto:
- Foundry Local on Azure Local
ms.topic: conceptual
ms.author: cwatson
author: cwatson-cat
ms.date: 04/20/2026
ai-usage: ai-assisted
customer intent: As a platform engineer or developer, I want to understand how the inference operator manages models in Foundry Local on Azure Local so that I can deploy and manage AI workloads effectively.
---

# Inference operator and model lifecycle in Foundry Local on Azure Local

This article explains how the inference operator works, how it manages the model lifecycle through Kubernetes custom resources, and how it reconciles resources to create inference endpoints. It also covers how generative and predictive model workloads differ and how to run inference against each type.

[!INCLUDE [foundry-local-preview](includes/foundry-local-preview.md)]

## What the inference operator does

The inference operator is a Kubernetes operator that simplifies deploying AI models for inference. It manages the complete lifecycle of model deployments by:

- Creating Kubernetes Deployments, Services, and Ingress resources.
- Configuring CPU and GPU workloads.
- Managing API key authentication and Entra ID token validation.
- Handling TLS certificates for secure communication.
- Caching model artifacts in a local OCI registry via the model store.

The operator runs a reconciliation loop on each custom resource. In addition to handling create, update, and delete events, a 30-second timer continuously monitors deployment health, tracks pod scheduling, and updates replica counts in the resource status.

## Custom resource definitions

The operator manages three CRDs. Two are user-facing, and one is internal.

| Resource | Kind | Short name | Purpose |
|----------|------|------------|---------|
| Model | `Model` | `mdl` | Defines a BYO (custom) model source and metadata. |
| ModelDeployment | `ModelDeployment` | `mdep` | Creates a running inference endpoint with all child resources. |
| StoreModel | `StoreModel` | `sm` | Internal. Tracks model caching state in the local OCI registry. |

By separating model definition from deployment, you can:

- Reuse model definitions across multiple deployments.
- Update deployments without changing model definitions.
- Manage models and deployments independently.

For quick deployments, you can skip creating a Model resource. The ModelDeployment can reference catalog models directly, and the operator resolves them from the catalog ConfigMap. For more information about how models are sourced, see [Model catalog and sourcing](concept-model-catalog.md).

## How the operator reconciles resources

When you create or update a ModelDeployment resource, the operator performs the following steps in order:

1. Validates the resource specification.
1. Resolves the model source (catalog lookup, Model CR reference, or inline custom config).
1. Ensures the model is cached locally by creating a StoreModel CR and waiting for the cache job to complete.
1. Selects the container image based on workload type, compute type, runtime, and model source.
1. Generates API key secrets for authentication.
1. Builds and creates child resources: Deployment, Service, nginx ConfigMap, Certificate (when TLS is enabled), and optionally Ingress.
1. Sets owner references on all child resources so they're garbage-collected when the ModelDeployment is deleted.
1. Updates the resource status with endpoint information.

### Child resources created per ModelDeployment

Use this table to see which Kubernetes resources the operator creates for each deployment and what each resource does.

| Resource | Always created | Purpose |
|----------|---------------|---------|
| Deployment | Yes | Runs the inference pods. |
| Service | Yes | Provides a ClusterIP endpoint. |
| Secret | Yes | Stores generated API keys. |
| ConfigMap | Yes | nginx sidecar configuration for auth and routing. |
| Certificate | When TLS is enabled | cert-manager Certificate for TLS termination. |
| Ingress | When `endpoint.enabled: true` | External path-based routing. |
| StoreModel | Yes | Triggers and tracks model caching in the local OCI registry. |

### ModelDeployment lifecycle states

A ModelDeployment moves through a set of lifecycle states that show where it is in provisioning, update, and cleanup.

| State | Description |
|-------|-------------|
| `Pending` | Resource created, waiting for processing. |
| `Creating` | Operator is creating child resources and waiting for model cache. |
| `Running` | All replicas are ready and serving traffic. |
| `Updating` | Applying configuration changes. |
| `Error` | Something went wrong. Check the `status.message` and `status.conditions` fields. |
| `Terminating` | Resource is being deleted. The operator cleans up child resources through owner references. |

### Model phases

Model CRs track their own lifecycle:

| Phase | Description |
|-------|-------------|
| `Pending` | Initial state, waiting for validation. |
| `Available` | Model definition is valid and ready for use in a ModelDeployment. |
| `Error` | Validation failed. Check `status.message`. |

## Generative models

Generative AI models create new content in response to prompts. Foundry Local on Azure Local supports generative text models for conversations, question answering, and text generation tasks. Both CPU and GPU hardware are supported. Two runtime engines are available: the default ONNX-GenAI engine (CPU or GPU) and the vLLM engine (GPU only) for high-throughput scenarios. You can select the runtime by using the `spec.runtime` field on the `ModelDeployment` object.

### Run generative inference

Generative model endpoints follow OpenAI-compatible request patterns. The inference endpoint is `/v1/chat/completions`.

```bash
curl -X POST https://<your-domain>/phi-3.5-gpu/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $API_KEY" \
  -d '{
    "model": "Phi-3.5-mini-instruct-cuda-gpu:1",
    "messages": [
      {"role": "system", "content": "You are a helpful assistant."},
      {"role": "user", "content": "What is the capital/majory city of France?"}
    ],
    "temperature": 0.7,
    "max_tokens": 100
  }'
```

**Notes:**

- **URL**: With an ingress controller, use the ingress IP or DNS. Without ingress, use `kubectl port-forward` and set the URL to 127.0.0.1.
- **Authorization header**: The inference operator generates API keys stored in a Kubernetes Secret. Pass the key as a Bearer token.
- **Response**: Standard OpenAI Chat Completions JSON response. The generated text is in `choices[0].message.content`.

## Predictive models

Predictive AI inference runs ONNX-based machine learning models for classification, regression, object detection, and other ML tasks.

Key capabilities:

- **ONNX Runtime**: Execute models in ONNX format, compatible with PyTorch, TensorFlow, and scikit-learn.
- **CPU and GPU execution**: Deploy on CPU for cost efficiency or GPU for higher throughput.
- **Batch processing**: Process multiple requests with configurable batch sizes (default: 32).
- **BYO model support**: Load custom ONNX models from ORAS-compatible registries.

The catalog doesn't include predictive models. To deploy predictive models, use BYO methods. For more information, see [Run inference on Foundry Local on Azure Local](how-to-run-inference.md).

### Run predictive inference

Predictive models use the `/v1/predict` endpoint. This example sends a base64-encoded image:

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

## GPU deployment options

When deploying on GPU nodes, the ModelDeployment supports:

- **`resources.limits.gpu`**: Set the number of GPUs (1-8) for the pod. Required when `compute: gpu`.
- **`skipGpuResource`**: When true, the operator doesn't set the `nvidia.com/gpu` resource limit. Use with `nodeSelector` to target GPU nodes where GPU allocation is managed externally.
- **`nodeSelector`**: Target specific nodes by label. Required when `skipGpuResource` is true.
- **`tolerations`**: Tolerate GPU node taints (NoSchedule, PreferNoSchedule, NoExecute).

For full field definitions and YAML examples, see [ModelDeployment and operator configuration reference](reference-model-deployment-operator.md).

## Configuration

The operator reads its configuration from a ConfigMap mounted at `/etc/inference-operator/config.yaml`.

```
Helm values.yaml --> ConfigMap --> /etc/inference-operator/config.yaml --> OperatorConfig
```

Key configuration areas:

| Area | What it controls |
|------|-----------------|
| images | Container image references for each workload/compute/source combination. |
| tls | TLS toggle, certificate durations, ports, and sidecar settings. |
| ingress | Default ingress class, path template, and rewrite settings. |
| catalog | Catalog ConfigMap name, namespace, and lazy registration toggle. |
| storeModel | Local registry URL, cache job timeout, and poll interval. |
| authentication | App-level authentication toggle. |

For the full configuration fields and example, see [ModelDeployment and operator configuration reference](reference-model-deployment-operator.md#inference-operator-configuration).

## Related content

- [Model catalog and sourcing in Foundry Local on Azure Local](concept-model-catalog.md)
- [StoreModel and model caching in Foundry Local on Azure Local](concept-model-caching.md)
- [Inference runtimes in Foundry Local on Azure Local](concept-inference-runtimes.md)
- [Automatic GPU inference tuning in Foundry Local on Azure Local](concept-gpu-inference-planner.md)
- [Deploy Foundry Local on Azure Local](deploy-foundry-local-on-azure-local.md)
- [Run inference on Foundry Local on Azure Local](how-to-run-inference.md)
- [ModelDeployment and operator configuration reference](reference-model-deployment-operator.md)
- [Inference API endpoints and payload reference](reference-inference-api-endpoints-payload.md)

