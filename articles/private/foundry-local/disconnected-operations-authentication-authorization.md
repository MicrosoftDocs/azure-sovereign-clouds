---
title: "Authentication and Authorization in Foundry Local on Azure Local in disconnected environments"
description: "Understand how Foundry Local secures inference endpoints in disconnected environmentsby using API key authentication and Active Directory (AD) infrastructure with Azure role-based access control (Azure RBAC) authorization."
ms.service: azure
ms.subservice: sovereign-private-clouds
appliesto:
- Foundry Local on Azure Local
ms.topic: how-to
ms.author: cwatson
author: cwatson-cat
ms.date: 05/28/2026
ai-usage: ai-assisted
customer intent: As a platform engineer or developer, I want to understand authentication and authorization options in Foundry Local in disconnected environments so that I can secure inference endpoints based on my environment requirements.
---

# Configure authentication and authorization for Foundry Local in disconnected environments

If you want to secure inference endpoints using role-based access control (RBAC), configure an application registration and assign the required Azure RBAC roles.

[!NOTE]
In disconnected environments, authentication is integrated with the Active Directory infrastructure configured in Azure Local rather than with public Microsoft Entra ID endpoint

## Configure RBAC Authentication

The following script creates an application registration and service principal, and grants the Arc-connected cluster managed identity read access to the cluster resource.

You might need to work with your Azure Local Disconnected administrator to complete these steps.

```ps1
# Step 1: Create an application registration
$DisplayName = "FoundryOnArc-Disconnected"
$subscriptionId = "<SUBSCRIPTION_ID>"
$resourceGroup = "<CONNECTED_K8S_CLUSTER_RG>"
$clusterName = "<CONNECTED_K8S_CLUSTER_NAME>"
$extensionName = "<FOUNDRY_K8S_EXTENSION_NAME>"

az login

Write-Host "Creating application registration..." -ForegroundColor Yellow
$app = az ad app create `
  --display-name $DisplayName `
  --sign-in-audience AzureADMyOrg `
  --requested-access-token-version 2 | ConvertFrom-Json

$appId = $app.appId
$objectId = $app.id
$tenantId = az account show --query tenantId -o tsv

Write-Host "Application created." -ForegroundColor Green
Write-Host "Application ID: $appId"
Write-Host "Tenant ID: $tenantId"
Write-Host "Object ID: $objectId"

# Step 2: Configure the identifier URI
$identifierUri = "api://$appId"

Write-Host "Configuring identifier URI..." -ForegroundColor Yellow
az ad app update `
  --id $appId `
  --identifier-uris $identifierUri | Out-Null

Write-Host "Identifier URI set to $identifierUri" -ForegroundColor Green

# Step 3: Create a service principal
Write-Host "Creating service principal..." -ForegroundColor Yellow
$sp = az ad sp create --id $appId | ConvertFrom-Json
$spObjectId = $sp.id

Write-Host "Service principal created: $spObjectId" -ForegroundColor Green

# Step 4: Grant the cluster managed identity Reader access
$clusterObjectId = az resource show `
  --ids "/subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.Kubernetes/connectedClusters/$clusterName" `
  --query identity.principalId `
  -o tsv

az role assignment create `
  --assignee-object-id $clusterObjectId `
  --assignee-principal-type ServicePrincipal `
  --role Reader `
  --scope "/subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.Kubernetes/connectedClusters/$clusterName/providers/Microsoft.KubernetesConfiguration/extensions/$extensionName"
```

## Assign Roles to Users

Grant users access to manage models and invoke inference endpoints by assigning the appropriate role on the Foundry extension resource.

```ps1
$subscriptionId = az account show --query id -o tsv
$resourceGroup = "<RESOURCE_GROUP>"
$clusterName = "<CLUSTER_NAME>"
$extensionName = "<FOUNDRY_EXTENSION_NAME>"
$userObjectId = "<YOUR_USER_OBJECT_ID>"

$extensionScope = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.Kubernetes/connectedClusters/$clusterName/providers/Microsoft.KubernetesConfiguration/extensions/$extensionName"

az role assignment create `
  --assignee $userObjectId `
  --role Contributor `
  --scope $extensionScope
```

## Assign Roles to Managed Identities

If a managed identity requires access to the Foundry Local extension, assign the appropriate Azure RBAC role to that identity.

```ps1
$extensionScope = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.Kubernetes/connectedClusters/$clusterName/providers/Microsoft.KubernetesConfiguration/extensions/$extensionName"

az role assignment create `
  --assignee "<MANAGED_IDENTITY_OBJECT_ID>" `
  --role Contributor `
  --scope $extensionScope
```
