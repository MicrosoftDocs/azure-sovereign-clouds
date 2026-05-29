---
title: "Deploy Your First Model and Run Inference on Foundry Local on Azure Local in a disconnected environment"
description: "Create your first model deployment and send inference requests by using the REST API in an existing Foundry Local environment."
ms.service: azure
ms.subservice: sovereign-private-clouds
appliesto:
- Foundry Local on Azure Local
ms.topic: quickstart
ms.author: cwatson
author: cwatson-cat
ms.date: 05/27/2026
ai-usage: ai-assisted
customer intent: As a platform engineer or developer, I want to deploy and run my first model in an existing disconnected Foundry Local environment on Azure Local so that I can validate AI inference on my on-premises cluster.
---

# Deploy your first model in a disconnected environment

After the Foundry Local extension expansion pack is installed, `phi-4-mini` CPU is already available as part of the base pack.

Use the following steps to create your first deployment from the catalog.

## Generate access token and request headers

```ps1
$DisplayName = "FoundryOnArc-Disconnected"
$app = az ad app list --display-name $DisplayName --query "[0]" -o json | ConvertFrom-Json
$appId = $app.AppId

$token = az account get-access-token --resource "$appId" --query accessToken -o tsv
$headers = @{ "Authorization" = "Bearer $token" }

# If using ingress:
$baseUrl = "https://<FOUNDRY_API_BASE_PATH>/inference-api"

# If not using ingress, use the direct API endpoint instead:
# $baseUrl = "https://<FOUNDRY_API_BASE_PATH>"
```

## Create your first deployment (phi-4-mini CPU)

```ps1
$namespace = "foundry-local-operator"
$deploymentName = "phi4-cpu-demo"

$body = @{
  name = $deploymentName
  spec = @{
    displayName = "Phi 4 CPU Demo"
    model = @{
      catalog = @{
        name = "phi-4-mini"
      }
    }
    workloadType = "generative"
    compute = "cpu"
    runtime = "onnx-genai"
    replicas = 1
    port = 5000
    authentication = @{
      enabled = $true
    }
    endpoint = @{
      enabled = $true
      path = "/phi4-cpu-demo(/|$)(.*)"
      pathType = "ImplementationSpecific"
    }
  }
} | ConvertTo-Json -Depth 20

Invoke-RestMethod `
  -Uri "$baseUrl/api/v1/namespaces/$namespace/deployments" `
  -Headers ($headers + @{ "Content-Type" = "application/json" }) `
  -Method POST `
  -Body $body
```

## Verify deployment

```ps1
Invoke-RestMethod `
  -Uri "$baseUrl/api/v1/namespaces/$namespace/deployments/$deploymentName" `
  -Headers $headers `
  -Method GET
```

Expected result:

* Deployment exists and returns successfully from the GET request.
* Deployment status moves to ready state after model cache and pod startup complete.
