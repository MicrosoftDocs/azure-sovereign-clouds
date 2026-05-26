---
title: What is GitHub Enterprise Local? (preview)
description: Deploy GitHub Enterprise Local on Azure Local for regulated industries needing data sovereignty and disconnected operations. Review architecture and start planning.
author: ronmiab
ms.author: robess
ms.reviewer: sushiljarwal
ms.date: 05/26/2026
ms.topic: concept-article
ms.service: azure
ms.subservice: sovereign-private-clouds
---

# What is GitHub Enterprise Local? (preview)

GitHub Enterprise Local enables organizations to run GitHub Enterprise Server (GHES) as a fully self-hosted DevOps platform on Azure Local infrastructure. This solution is designed for organizations that require data sovereignty, disconnected or air‑gapped operations, and full control over source code, CI/CD pipelines, and developer workflows.

GitHub Enterprise Local is deployed as a prebuilt virtual appliance on Azure Local and operates entirely within customer‑owned infrastructure. All repositories, metadata, artifacts, and execution remain on‑premises while preserving a GitHub‑consistent developer experience.

## Overview

GitHub Enterprise Local brings GitHub's enterprise developer platform into sovereign private cloud environments using Azure Local. It's intended for regulated industries such as government, defense, finance, healthcare, and critical infrastructure where public cloud usage is restricted or prohibited.

The solution leverages Azure Local for infrastructure lifecycle management while [GitHub Enterprise Server](https://docs.github.com/enterprise-server@3.20/get-started/onboarding/getting-started-with-github-enterprise-server) (GHES) delivers:

- Source code management

- Pull requests and code reviews

- Issues and project tracking

- GitHub Actions for CI/CD (via self‑hosted runners)

- GitHub Packages for artifact management

- GitHub Advanced Security

GitHub Enterprise Server runs without internet connectivity by default, enabling fully disconnected deployments.

## Why use GitHub Enterprise Local?

Organizations choose GitHub Enterprise Local for the following reasons:

- **Data sovereignty and compliance**: All code and artifacts stay on your infrastructure, supporting strict regulatory and jurisdictional requirements.

- **Disconnected operations**: Supports air‑gapped or intermittently connected environments without dependency on GitHub.com.

- **Enterprise‑grade DevOps**: Provides near feature consistency with GitHub.com, including Actions, Packages, and Advanced Security, fully behind the firewall.

- **Azure‑consistent operations**: Uses familiar Azure Local and Azure Arc operational models for VM lifecycle, monitoring, and infrastructure updates.

## GitHub Enterprise Local capabilities

### Core GitHub platform

- Private repositories and organizations.

- Pull requests, code reviews, and branch protection.

- Issues, wikis, and project collaboration.

### CI/CD and artifacts

- GitHub Actions with self‑hosted runners for fully offline pipelines.

- GitHub Packages supporting npm, NuGet, Maven, and container images.

### Actions storage and feature limitations

- GitHub Actions logs and artifacts require external object storage, such as Azure Blob Storage or an S3-compatible API endpoint.

### Security and compliance

- GitHub Advanced Security features, including code scanning, secret scanning, and dependency alerts.

- Full audit logging and compliance reporting.

- Integration with enterprise identity providers, such as SAML and Microsoft Entra ID.

The GHES virtual appliance delivers these capabilities entirely on your infrastructure.

## Architecture and deployment model

Deploy GitHub Enterprise Local by using the following model:

1.  **Infrastructure layer**

    - Azure Local Integrated Systems or Premier Solutions hardware

    - Azure Arc-enabled management for Azure Local infrastructure and VM lifecycle

1.  **GitHub appliance layer**

    - Prebuilt GHES VM image deployed as a virtual machine

    - Persistent data disks for repositories and metadata

1.  **Operations layer**

    - Azure Local manages VM availability and infrastructure updates

    - GitHub administrators manage application configuration, upgrades, user access, and ongoing maintenance (including keeping GitHub Enterprise Server updated to the latest supported version) via the GHES admin console

[High availability and replica‑based failover](https://docs.github.com/enterprise-server@3.20/admin/backing-up-and-restoring-your-instance/backup-service-for-github-enterprise-server/about-the-backup-service-for-github-enterprise-server#about-the-github-enterprise-server-backup-service) configurations can be implemented based on your requirements.

## Disconnected and hybrid deployment scenarios

Azure Local supports both connected and fully disconnected deployment modes. In a connected deployment, Azure Local integrates with Azure services to enable centralized monitoring, updates, and policy management. In disconnected environments, the Azure control plane and management services run locally within your environment. Organizations can operate in fully isolated or air-gapped scenarios while maintaining local management capabilities.

GitHub Enterprise Local operates independently of Azure Local's connectivity state. Organizations can deploy GitHub Enterprise Local in either connected or disconnected environments, and the connectivity mode of GitHub Enterprise Local doesn't need to match the Azure Local configuration. In connected deployments, GitHub Enterprise Local can integrate with external services such as object storage like Azure Blob Storage to support capabilities like GitHub Actions artifacts and package storage. In disconnected deployments, GitHub Enterprise Local runs entirely within your infrastructure, with all functionalities operating locally and without external service dependencies.

Together, this model allows you to adopt a connectivity posture that fits your requirements ranging from fully disconnected, to fully connected, to hybrid combinations without compromising sovereignty, control, or developer experience.

## AI-assisted developer experience

After deployment, **GitHub Enterprise Local** extends the developer experience with AI-assisted workflows tailored to both connected and disconnected environments.

- **Connected environments** leverage cloud-hosted AI (for example, GitHub Copilot and GitHub CLI) to enable code completion, chat, explanation, and workflow automation. This approach accelerates development while preserving existing IDE, repository, and CI/CD workflows.

- **Disconnected (air**‑**gapped) environments** maintain a similar experience by using **GitHub CLI–style workflows paired with local inference endpoints** (for example, Foundry Local). This approach ensures prompts, code context, and inference stay within your controlled boundaries.

- **Foundry Local** enables local model hosting on Azure Local, supporting chat, code assistance, scripting, and agentic workflows. This approach allows teams to preserve AI productivity while meeting sovereignty, compliance, and operational control requirements.

:::image type="content" source="media/github-local-overview/connected-disconnected-architecture.png" alt-text="Screenshot of the connected and disconnected architecture diagram for GitHub Enterprise Local, showing both cloud-connected and air-gapped deployment models." lightbox="media/github-local-overview/connected-disconnected-architecture.png":::

Together, these capabilities provide a **consistent, flexible AI developer experience** across cloud and on‑prem environments. This approach balances productivity with security and compliance.

## Security and sovereignty

GitHub Enterprise Local aligns with Azure Local security capabilities, including:

- Network isolation and firewall policies you define.

- FIPS‑validated cryptography through the underlying Azure Local platform.

- Identity, access, and auditing you control.

This model helps you meet stringent compliance frameworks while maintaining modern DevOps practices.

## GitHub Enterprise Local hardware requirements

Azure Local supports running GitHub Enterprise Local on both Integrated and Premier hardware solutions, provided sufficient capacity is available. Plan compute, memory, storage, and network resources accordingly. 

GHES provides guidance on VM sizing based on your use cases and requirements, such as: 

- Number of developers 

- Repository size and growth 

- CI/CD pipeline frequency 

- Artifact storage requirements 

[Minimum recommended requirements](https://docs.github.com/enterprise-server@3.15/admin/monitoring-and-managing-your-instance/updating-the-virtual-machine-and-physical-resources/increasing-storage-capacity#minimum-recommended-requirements)  

GitHub Enterprise Local might be supported on both Integrated Systems and Premier Solutions if there's sufficient capacity to run the GHES VM. Current offerings are available at [Azure Local Solutions \| Catalog](https://azurelocalsolutions.azure.microsoft.com/#/catalog).

## Get started with GitHub Enterprise Local

GitHub Enterprise Local is now in public preview. For information about it, contact your Microsoft account team or visit [GitHub Enterprise Local Preview Sign-Up](https://forms.office.com/pages/responsepage.aspx?id=v4j5cvGGr0GRqy180BHbRw9dxZ_D1b1FnjqEPJdlIB5UQTUxRUhUWExXNFQzNFRRSUJDVDFTSkFINC4u&origin=lprLink&route=shorturl). This survey helps Microsoft identify interested organizations and assess their readiness and requirements for GitHub Enterprise Local. Microsoft reviews submissions on a rolling basis, with evaluations conducted every two weeks. Selected participants are contacted for onboarding into the public preview. Feedback collected during this process helps shape future product direction.

## Deployment overview

GitHub Enterprise Local supports both **single**‑**node** and **multi-node** Azure Local deployment options to match your needs. Single node Azure Local runs GHES as a standalone VM, ideal for preview, proof of concept, and low‑risk scenarios focused on simplicity and cost efficiency. For production-oriented deployments, the same single GHES VM can run on a **multi-node Azure Local cluster**, where Azure Local provides VM‑level high availability and failover, while GHES itself remains a single, non‑clustered instance.

A typical deployment flow includes:

- Prepare Azure Local infrastructure

- **GitHub Enterprise Local VM image download location:** When you download the GitHub Enterprise Local VM image, place it in a location that an Azure Local node can see and access.

- To save time, download the GitHub Enterprise Local VM image directly to the final location you provide during deployment (the deployment workflow copies it again into Azure Local storage).

- Deploy the GitHub Enterprise Local VM image by using the PowerShell script provided along with the file

- Complete initial GHES configuration via the admin console

- Integrate identity providers and configure organizations

- Enable Actions, Packages, and Advanced Security

## Billing overview

To run **GitHub Enterprise Local**, you must purchase a **GitHub Enterprise license**. The license follows a **seat**‑**based billing model**. Charges are calculated monthly based on the number of **active users consuming licenses**. The model uses a **unique**‑**user model** where each user consumes a single seat regardless of how many environments they access. For more information, see [Billing for GitHub Enterprise](https://docs.github.com/en/billing/concepts/enterprise-billing/billing-for-enterprises) and [GitHub Enterprise pricing](https://azure.microsoft.com/pricing/details/githubenterprise/).

In contrast, **Azure Local** uses an **infrastructure‑based billing model** rather than a user‑based one. You're billed **per physical CPU core per month** for the Azure Local host, independent of the number of developers or applications running on top of the platform. For more information, see [Azure Local billing and payment](/azure/azure-local/concepts/billing) and [Azure Local pricing](https://azure.microsoft.com/pricing/details/azure-local/).

As a result, the **total cost of ownership** is made up of two clearly separated components: **GitHub's user‑based application licensing** and **Azure Local's core‑based infrastructure charges**. This separation provides transparency between software licensing costs and underlying platform consumption.

## Next steps

- Plan hardware and [capacity requirements](https://docs.github.com/en/enterprise-server@3.15/admin/monitoring-and-managing-your-instance/updating-the-virtual-machine-and-physical-resources/increasing-storage-capacity#minimum-recommended-requirements) for GitHub Enterprise Server.

- Review the [GitHub Advanced Security](https://docs.github.com/en/enterprise-server@3.20/get-started/learning-about-github/about-github-advanced-security) and protection offering.

- Prepare for [public preview](https://forms.office.com/pages/responsepage.aspx?id=v4j5cvGGr0GRqy180BHbRw9dxZ_D1b1FnjqEPJdlIB5UQTUxRUhUWExXNFQzNFRRSUJDVDFTSkFINC4u&origin=lprLink&route=shorturl) onboarding.
