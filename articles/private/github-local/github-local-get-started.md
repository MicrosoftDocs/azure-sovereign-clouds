---
title: Get started with GitHub Enterprise Local (preview)
description: Learn how to onboard to the GitHub Enterprise Local public preview and deploy GitHub Enterprise Server on Azure Local.
author: ronmiab
ms.author: robess
ms.reviewer: sushiljarwal
ms.date: 06/02/2026
ms.topic: how-to
ms.service: azure
ms.subservice: sovereign-private-clouds
---

# Get started with GitHub Enterprise Local (preview)

This article explains how to onboard to the public preview and deploy GitHub Enterprise Local on Azure Local.

[!INCLUDE [sovereign-private-clouds-preview.md](../includes/sovereign-private-clouds-preview.md)]

## Prerequisites

GitHub Enterprise Local is in public preview. To express interest, contact your Microsoft account team or submit the [GitHub Enterprise Local Preview Sign-Up](https://forms.office.com/pages/responsepage.aspx?id=v4j5cvGGr0GRqy180BHbRw9dxZ_D1b1FnjqEPJdlIB5UQTUxRUhUWExXNFQzNFRRSUJDVDFTSkFINC4u&origin=lprLink&route=shorturl).

Microsoft reviews survey submissions on a rolling basis, with evaluations conducted every two weeks. Approved organizations are contacted with onboarding details.

> [!IMPORTANT]
> Microsoft provides the deployment PowerShell script only to approved public preview participants after they submit the onboarding survey and are selected for preview onboarding.

Azure Local supports running GitHub Enterprise Local on both Integrated and Premier hardware solutions, provided sufficient capacity is available. Plan compute, memory, storage, and network resources accordingly.

GHES provides VM sizing guidance based on your use cases and requirements, such as:

- Number of developers

- Repository size and growth

- CI/CD pipeline frequency

- Artifact storage requirements

For more information, see the [Minimum recommended requirements](https://docs.github.com/enterprise-server@3.15/admin/monitoring-and-managing-your-instance/updating-the-virtual-machine-and-physical-resources/increasing-storage-capacity#minimum-recommended-requirements).

GitHub Enterprise Local might be supported on both Integrated Systems and Premier Solutions if there's sufficient capacity to run the GHES VM. Current offerings are available at [Azure Local Solutions \| Catalog](https://azurelocalsolutions.azure.microsoft.com/#/catalog).

## Deploy GitHub Enterprise Local

GitHub Enterprise Local supports both single-node and multi-node Azure Local deployment options. Single-node Azure Local runs GHES as a standalone VM for preview and proof-of-concept scenarios. For production-oriented deployments, the same single GHES VM runs on a multi-node Azure Local cluster. Azure Local provides VM-level high availability and failover, while GHES itself remains a single, non-clustered instance.

A typical deployment includes the following steps:

1. **Prepare Azure Local infrastructure**. Ensure you set up an Azure Local environment with sufficient capacity to run the GHES VM image.

1. **Download the GitHub Enterprise Local VM image**. Place the image in a location that an Azure Local node can see and access. To reduce deployment time, download the image directly to the target location specified during deployment, because the deployment process copies the image into Azure Local storage.

1. **Deploy the GitHub Enterprise Local VM image**. Use the PowerShell script provided during public preview onboarding.

1. **Complete GHES setup by using the admin console**. Use the admin console to complete the initial configuration.

1. **Integrate identity providers and configure organizations**. Set up identity providers and configure your organizations.

1. **Enable Actions, Packages, and Advanced Security**. Configure these features as needed.

## Next steps

- For architecture and capability guidance, see [What is GitHub Enterprise Local? (preview)](github-local-overview.md).

- Review [Azure Local overview](../azure-local/azure-local-overview.md).

- Plan connectivity using [connected operations](../azure-local/connected-operations-overview.md) or [disconnected operations](../azure-local/disconnected-operations-overview.md).
