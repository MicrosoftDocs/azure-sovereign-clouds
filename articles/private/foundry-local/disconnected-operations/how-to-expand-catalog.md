---
title: "Expand Model Catalog for Foundry Local on Azure Local Deployment in Disconnected Mode"
description: "Expand the Model Catalog for your Foundry Local on Azure Local deployment in a disconnected environment."
ms.service: azure
ms.subservice: sovereign-private-clouds
appliesto:
- Foundry Local on Azure Local
ms.topic: how-to
ms.author: cwatson
author: cwatson-cat
ms.date: 05/27/2026
ai-usage: ai-assisted
customer intent: As a platform engineer, I want to expand the model catalog for my Foundry Local disconnected environment.
---

# Expand model catalog in disconnected mode

To make additional models available in a disconnected environment, download and install the corresponding model expansion packs into the Azure Local disconnected deployment.

Each model is distributed as a separate expansion pack. After installation, the model artifacts are imported into the `edgeartifacts` container registry.

After each model expansion pack installation, verify the pack state on the `Azure Local Disconnected Operations` machine:

```ps1
Get-ApplianceExpansionPackDetails
```

- Confirm the newly installed model pack is in the `Installed` state before running model sync.

After the expansion packs have been installed successfully, trigger a model synchronization operation to refresh the model catalog and make the newly installed models available for use.

Use the following PowerShell command to initiate the synchronization:

```ps1
$baseUrl = "https://<FOUNDRY_API_BASE_PATH>"

Invoke-RestMethod `
  -Uri "$baseUrl/api/v1/models/sync" `
  -Headers $headers `
  -Method POST
```

After the synchronization completes, the newly installed models appear in the model catalog and you can enable them for deployment and inference workloads.
Verify model sync completed and the model is visible in the catalog:

```ps1
Invoke-RestMethod `
  -Uri "$baseUrl/api/v1/models" `
  -Headers $headers `
  -Method GET
```

**Package naming convention**

`azurelocal.pxp.foundrylocal.<MODEL_NAME>.model.<PUBLISH_VERSION>.zip`

Example:

`azurelocal.pxp.foundrylocal.phi-3.5-mini.gpu.model.1.2605.12.zip`

## Related content

- [Troubleshoot Foundry Local on Azure Local in disconnected environments](how-to-troubleshoot.md)