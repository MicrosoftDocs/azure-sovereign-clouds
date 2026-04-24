---
title: Configure your Dataverse and Power Platform environments for more data sovereignty
description: Configure your Dataverse and Power Platform environments for more data sovereignty
author: lavanyapg
ms.topic: overview
ms.date: 03/24/2025
ms.author: kerabun
ms.reviewer: lsuresh
ms.subservice: sovereign-public-clouds
ms.collection: 
    - microsoftcloud-sovereignty
---

# Data sovereignty in Dataverse and Power Platform environments

The [Microsoft Power Platform admin center](https://admin.powerplatform.microsoft.com/home) centralizes management of environments and settings for Power Platform. It helps you manage both data residency and data access controls to support your sovereign requirements. [Tenant settings](/power-platform/admin/control-environment-creation) in the Admin Center let you control how environments in your tenant are created and managed. You can regulate which administrators have the ability to create new environments, limiting this capability to only Global, Dynamics 365, and Power Platform admins. This setting enhances your control over the location, method, and personnel accessing your data assets.

## Enable managed environments

Managed environments provide extensive capabilities to help you configure Dataverse and Power Platform and reduce the risk of inadvertent data leakage. These capabilities help align with your security and sovereignty requirements. The capabilities include IP Cookie Binding, Customer Lockbox, [IP firewall](/power-platform/admin/ip-firewall), customer-managed keys, and more.

In the Admin Center, you can administer data policies for your Managed Environments. Use data policies to protect all the environments in your tenant. For example, see [Data Loss Prevention policy](/power-platform/admin/managed-environment-data-policies).

In the Admin Center, you can configure the [solution checker](/power-apps/maker/data-platform/use-powerapps-checker) in Managed Environments to enforce checks on your solutions against a set of best practice rules and identify problematic patterns. This check can help you prevent poor data management practices that result in data access or distribution that violates your sovereign requirements.

Managed environments is also previewing [default environment routing](/power-platform/admin/default-environment-routing?tabs=ppac), a new sovereign-control-supporting feature. When a new maker comes to Power Apps, they're automatically routed into their own, personal developer space rather than into a shared default environment. They can build without risk of other makers accessing their apps or data.

For more information, see [Enable Managed Environments - Power Platform](/power-platform/admin/managed-environment-enable?source=recommendations) and [Data policies - Power Platform](/power-platform/admin/managed-environment-data-policies).

## Data residency and multi-geo deployments

When you sign up for Power Platform services, you choose a country/region that maps to the most suitable **Azure geography** where a Power Platform deployment exists.

### Data residency in Power Platform 

Data residency deals with the physical location where data is stored and processed. **Data residency** ensures that customer data is stored in the tenant's assigned Azure geography (or home geo).

Data residency requirements are a common concern for public sector customers, who often request that Microsoft limit where different types of data are stored and processed. Power Platform provides controls and mechanisms to ensure that both *personal data* and *customer data* are protected. These controls restrict the services and regions that end users can use and enforce service configuration to help customers achieve their data residency needs.

### Geography selection

If you're a global organization, **multi-geo deployments** let you store data in specific regions to comply with local regulations. When you sign up for Power Platform services, your tenant's selected country/region is mapped to the most suitable Azure geography where a Power Platform deployment exists. For multi-geo tenants, you can specify the geo for an environment. In multi-geo deployments, metadata remains in the home geo, while metadata and actual data resides in the remote geo. Microsoft can replicate data to other regions for data resiliency. For more information, see [Data storage and governance in Power Platform](/power-platform/admin/security/data-storage).

### Tenant isolation

To reduce the chance of unauthorized data sharing, set up Power Platform with [tenant isolation ON](/power-platform/admin/cross-tenant-restrictions), to make sure that only a limited number of tenants (or none) can connect with their sovereign tenant. Prevent inbound and outbound connections that go beyond the sovereignty boundary. For example, your policy controls can indicate that it's acceptable for your tenant to be connected by other tenant IDs that are within your sovereign boundary, but not regions outside that boundary. For more information on tenant isolation, see: [Restrict cross-tenant inbound and outbound access - Power Platform](/power-platform/admin/cross-tenant-restrictions).

### Backup and failover

Microsoft might replicate non-personal data, such as employee authentication information, to other regions for data resiliency. However, personal and customer data isn't ever replicated or moved outside the geo. [System backups](/power-platform/admin/backup-restore-environments) of production environments occur automatically and are geo-redundant for resiliency and availability. In some cases, the backup region can be outside of your sovereign boundary.

To learn more about business continuity and disaster recovery, failover, and fallback processes for Dataverse and F&O apps, see: [Business continuity and disaster recovery for Dynamics 365 SaaS apps - Power Platform](/power-platform/admin/business-continuity-disaster-recovery).

## Data loss prevention policies

[Power Platform Data Loss Prevention (DLP) policies](/power-platform/admin/prevent-data-loss) can act as guardrails to help enforce data residency requirements. [DLP policies](/power-platform/admin/wp-data-loss-prevention) can also help enforce which connectors can communicate with each other to prevent sensitive business data from being inadvertently or deliberately transferred out of the sovereign region. By default, all connectors are initially assigned to the non-business (personal-use) data group. 

To reduce the risk of sensitive information leaking out of the sovereign environment, [connectors for sensitive data](/training/modules/implementation-recommendations/4-implement) should be assigned to the Business data group. To further protect Dynamics 365 environments, these connectors should also be [assigned to the Business data group](/training/modules/implementation-recommendations/5-customer-experience). For more information on managing DLP policies, see  [Manage data loss prevention (DLP) policies - Power Platform](/power-platform/admin/prevent-data-loss).
  
## Dual-Write

[Dual-write](/dynamics365/fin-ops-core/dev-itpro/data-entities/dual-write/security-roles) provides tightly coupled, bidirectional integration between finance and operations apps and Dataverse. Data changes in finance and operations apps can cause writes to Dataverse, and data changes in Dataverse can cause writes to finance and operations apps. This automated data flow provides an integrated user experience across the apps.

Dual-write requires specific security roles and permissions to work as expected. Add all Microsoft Dataverse users to the dual-write runtime user and dual-write app user security roles. If you don't properly manage these roles, it could potentially lead to unauthorized access. 

The data residency and compliance requirements can vary based on the geographic location where the data is stored and processed. Ensure that the data flow complies with all relevant regional and international data protection regulations. For more information, see [Dual-write](/dynamics365/fin-ops-core/dev-itpro/data-entities/dual-write/dual-write-overview) and [Set up dual-write security roles and permissions](/dynamics365/fin-ops-core/dev-itpro/data-entities/dual-write/security-roles).

For more information, see [Governance setting to control anonymous access to Dataverse data in Power Pages website](https://powerpages.microsoft.com/blog/governance-control-to-disable-anonymous-access-to-dataverse-data-in-power-pages-websites/).



