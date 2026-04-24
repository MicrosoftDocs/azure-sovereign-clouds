---
title: Configure your Dynamics 365 Business Central environments for more data sovereignty
description: Configure your Dynamics 365 Business Central environments for more data sovereignty
author: jobulsin
ms.topic: overview
ms.date: 08/13/2025
ms.author: jobulsin
ms.reviewer: syalandur
ms.collection: 
    - microsoftcloud-sovereignty
---

# Data sovereignty in Dynamics 365 Business Central

Use the [Dynamics 365 Business Central administration center](/dynamics365/business-central/dev-itpro/administration/tenant-admin-center) to manage environments and settings for Dynamics 365 Business Central. It helps you control data residency and data access to support your sovereignty requirements.

## Data residency and multi-geo deployments

When you create a Dynamics 365 Business Central environment, you select the environment localization. The environment localization determines the Azure Geo where your environment database resides. Any tenant can create environments with any localization, regardless of the tenant's home geo.

### Data residency in Dynamics 365 Business Central

Data residency deals with the physical location where data is stored and processed. **Data residency** means customer data is stored in the environment's assigned Azure geography (or home geo).

Public sector customers frequently have data residency requirements and need Microsoft to control where various types of data are stored and processed. To address these needs, Dynamics 365 Business Central offers controls and tools that help safeguard both *personal data* and *customer data*, limit the services and regions available to end users, and configure service settings to support customers' data residency requirements.

### Geography selection

If you're a global organization, **multi-geo deployments** lets you store data in specific regions to meet local regulations. When you sign up for Dynamics 365 Business Central, your tenant's selected countries/regions sets the environment localization of the initial production environment. Admins can create more environments with different localizations to deploy in other Azure geographies from the Dynamics 365 Business Central administration center. In multi-geo deployments, environment data stays in the environment's geo, which might be different from the tenant's [home or default geo](/microsoft-365/enterprise/m365-dr-overview) used by other Microsoft services. While an environment database mainly stays in a single region within a geo, Microsoft might store a copy of the data in paired regions within the same geo for data resiliency. For more information, see [Azure regions](/azure/reliability/regions-list).

### Backup and failover

Microsoft might replicate nonpersonal data to other regions to keep the service available during an outage. But personal and customer data isn't ever replicated or transferred outside the geo where it's stored. [System backups](/dynamics365/business-central/dev-itpro/administration/tenant-admin-center-backup-restore) of environments happen automatically and are geo-redundant for resiliency and availability. In rare cases, because of Azure infrastructure limitations, system backups can be zone-redundant instead of geo-redundant. For more information, see [Azure regions](/azure/reliability/regions-list).

To learn more about business continuity, disaster recovery, failover, and fallback processes for Dynamics 365 Business Central, see [Business continuity and disaster recovery](/dynamics365/business-central/dev-itpro/service-overview#business-continuity-and-disaster-recovery-bcdr).

### EU Data Boundary

Dynamics 365 Business Central environments with a localization of a countries/regions in the European Union (EU) or the European Free Trade Association (EFTA) are within the [EU Data Boundary](/privacy/eudb/eu-data-boundary-learn) (EUDB). The EUDB is a geographic boundary where Microsoft commits to store and process certain types of data, including customer data. This data isn't ever stored or processed outside the EUDB.

## Azure Policy and sovereign guardrails

Azure services that integrate with Dynamics 365 Business Central can use Azure Policy to enforce sovereign controls on those services. For example, you can make sure no data is deployed outside allowed regions, or require encryption. For more information, see [Overview of Sovereign Landing Zone (SLZ)](overview-sovereign-landing-zone.md).

## See also

* [Access controls for Dynamics 365 Business Central](access-controls-d365-business-central.md) </br>

* [Security controls in Dynamics 365 Business Central](security-controls-d365-business-central.md)
