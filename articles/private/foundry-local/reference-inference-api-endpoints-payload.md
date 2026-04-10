---
title: "Inference API endpoints and payload reference for Foundry Local on Azure Local"
titleSuffix: Foundry Local on Azure Local
description: "Reference for inference API endpoints, request payload formats, and authentication header options in Foundry Local on Azure Local."
ms.service: azure
ms.subservice: sovereign-private-clouds
appliesto:
- Foundry Local on Azure Local
ms.topic: reference
ms.author: cwatson
author: cwatson-cat
ms.date: 03/25/2026
ai-usage: ai-assisted
customer intent: As a developer, I want a complete reference for inference API endpoints and payload formats in Foundry Local on Azure Local so that I can integrate AI models into my applications.
---

# Inference API endpoints and payload reference for Foundry Local on Azure Local

This article is a reference for inference API endpoints, request and response payload formats, and authentication header options for models deployed on Foundry Local on Azure Local.

[!INCLUDE [foundry-local-preview](includes/foundry-local-preview.md)]

## API endpoints

Each deployed model exposes the following endpoints. Replace `<base-url>` with your ingress address or internal cluster URL.

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/health` | GET | Liveness check. Returns `200 OK` when the service is running. |
| `/ready` | GET | Readiness check. Returns `200 OK` when the model is loaded and ready to serve requests. |
| `/v1/model` | GET | Model information. Returns metadata about the loaded model. |
| `/v1/chat/completions` | POST | Generative inference. Use for chat and text generation workloads. |
| `/v1/predict` | POST | Predictive inference. Use for ONNX-based classification, regression, and other ML tasks. |

## Authentication

All endpoints require an API key. Include it in your request using one of these header formats:

| Header format | Example |
|--------------|---------|
| Bearer token (standard) | `Authorization: Bearer <api-key>` |
| X-API-Key header | `X-API-KEY: <api-key>` |
| api-key header (OpenAI-compatible) | `api-key: <api-key>` |

All three formats are equivalent. The NGINX sidecar proxy validates the key and rejects requests that don't match the deployment's primary or secondary key with `401 Unauthorized`.

For information about retrieving and rotating API keys, see [Configure TLS and authentication for Foundry Local on Azure Local](how-to-configure-tls-authentication.md).

## Generative inference request examples

The `/v1/chat/completions` endpoint follows OpenAI Chat Completions conventions.

### Authorization: Bearer

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

### X-API-KEY

```bash
curl -X POST https://<your-domain>/phi-3.5-gpu/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "X-API-KEY: $API_KEY" \
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

### api-key

```bash
curl -X POST https://<your-domain>/phi-3.5-gpu/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "api-key: $API_KEY" \
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

### Generative response

```json
{
  "model": "Phi-3.5-mini-instruct-cuda-gpu:1",
  "choices": [{
    "message": {
      "role": "assistant",
      "content": "The capital/major city of France is Paris."
    },
    "index": 0,
    "finish_reason": "stop"
  }],
  "object": "chat.completion"
}
```

## Predictive inference request examples

The `/v1/predict` endpoint accepts ONNX model inputs. The exact payload structure depends on your model's input schema.

### Image input (base64-encoded)

#### [PowerShell](#tab/predict-powershell)

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

#### [Bash](#tab/predict-bash)

```bash
BASE64_IMAGE=$(base64 -w 0 <PATH_TO_IMAGE_FILE>)

curl -k -X POST "https://<URL>/v1/predict" \
  -H "Content-Type: application/json" \
  -H "X-API-KEY: $API_KEY" \
  -d "{
    \"items\": [{
      \"content_type\": \"image/jpeg\",
      \"encoder\": \"base64\",
      \"data\": \"$BASE64_IMAGE\"
    }]
  }"
```

---

> [!NOTE]
> The `-k` flag (curl) and `-SkipCertificateCheck` (PowerShell) skip certificate validation for self-signed certificates. In production, configure proper TLS certificates.

## Related content

- [Run inference on Foundry Local on Azure Local](how-to-run-inference.md)
- [Configure TLS and authentication for Foundry Local on Azure Local](how-to-configure-tls-authentication.md)
- [Inference operator and model lifecycle](concept-inference-operator.md)
- [ModelDeployment and operator configuration reference](reference-model-deployment-operator.md)

