---
title: Transparency controls in Dataverse and Power Platform
description: Provide customers with transparency into what is happening with their resources and data in Dataverse and Power Platform.
author: lavanyapg
ms.topic: overview
ms.date: 03/24/2025
ms.author: kerabun
ms.reviewer: lsuresh
ms.subservice: sovereign-public-clouds
ms.collection: 
    - microsoftcloud-sovereignty
    - microsoftcloud-seo-priority
---

# Transparency controls in Dataverse and Power Platform

Many sovereign compliance policies include requirements for transparency, auditing, and governance. These policies ensure visibility into and recording of access to data resources and particularly of changes made to the data in the environment.

## Auditing and monitoring 

To provide this transparency, you can enable auditing logging in the Power Platform Admin Center. Detailed guidance on managing audit logs is available in [Manage Dataverse auditing - Power Platform | Microsoft Learn](/power-platform/admin/manage-dataverse-auditing). Apps portal. Audit logs are stored in the Dataverse, to help maintain data residency. In some cases, these audit logs and records are subject to sovereignty controls themselves.

Audit logs for DLP activities, another important part of the transparency story, are tracked from the [Microsoft 365 Security and compliance portal - Microsoft Learn](/microsoft-365/?view=o365-worldwide&preserve-view=true).

To provide more operational transparency, you can [Enable activity logging for Power Apps - Power Platform | Microsoft Learn](/power-platform/admin/logging-powerapps) through the Microsoft Purview Compliance Portal.

## Managing access by Microsoft engineers

On rare occasions, it's necessary for a Microsoft engineer to directly access a customer's Dataverse or other Dynamics resources, typically in response to a customer-support request or some operational failure. To reduce the risk of inadvertent or intentional data access, Microsoft imposes strict controls on how these accesses are done, and logging access.

### Just-In-Time Access Controls

In situations where an engineer needs to access production or customer resources, they must submit a request. They make this request through Microsoft's internal Just-In-Time (JIT) access authorization control system and provide justification before internal approval is granted. JIT also creates a record of the access request. If an authorized JIT approver grants the request, then the engineer receives access to the designated resource for a limited period.

### Lockbox

For your Managed Environments, you can use [Microsoft Lockbox for Power Platform and Dynamics 365 - Power Platform | Microsoft Learn](/power-platform/admin/about-lockbox) to control whether the engineer can access many of your Dataverse and Power Platform resources. When an engineer makes a data access request through JIT, if it's approved internally, then you receive an email notifying you of the request. You can then choose to permit or deny access. If you deny the access, then the engineer's access request is rejected and they aren't granted access to your resources. For more information, see [Lockbox -Exclusions - Power Platform | Microsoft Learn](/power-platform/admin/about-lockbox#exclusions). 

All updates to access requests are logged and auditable. For more information, see [Audit lockbox requests - Power Platform | Microsoft Learn](/power-platform/admin/about-lockbox#audit-lockbox-requests). 

To support both sovereign access control and transparency requirements, customers should use [Lockbox - Power Platform | Microsoft Learn](/power-platform/admin/about-lockbox#summary) for all supported services. Lockbox support is also available for [Finance & Operations - Dynamics 365 | Microsoft Learn](/dynamics365/fin-ops-core/dev-itpro/sysadmin/customer-lockbox) apps and for [Customer Insights (preview) - Dynamics 365 | Microsoft Learn](/dynamics365/customer-insights/data/security-lockbox).

## See also

[Power Apps activity logging - Power Platform | Microsoft Learn](/power-platform/admin/logging-powerapps)

[Manage Dataverse auditing - Power Platform | Microsoft Learn](/power-platform/admin/manage-dataverse-auditing)

[Manage data loss prevention (DLP) policies - Power Platform | Microsoft Learn](/power-platform/admin/prevent-data-loss)

[Use Customer Lockbox to manage secure access to customer data - Finance & Operations | Dynamics 365 | Microsoft Learn](/dynamics365/fin-ops-core/dev-itpro/sysadmin/customer-lockbox)

[Securely access customer data with Customer Lockbox (Preview) - Dynamics 365 Customer Insights | Microsoft Learn](/dynamics365/customer-insights/data/security-lockbox)

[Securely access customer data using Customer Lockbox in Power Platform and Dynamics 365 - Power Platform | Microsoft Learn](/power-platform/admin/about-lockbox)





