---
title: Transparency controls in Dynamics 365 Business Central
description: Provide customers with transparency into what is happening with their resources and data in Dynamics 365 Business Central.
author: jobulsin
ms.topic: overview
ms.date: 08/13/2025
ms.author: jobulsin
ms.reviewer: lsuresh
ms.collection: 
    - microsoftcloud-sovereignty
    - microsoftcloud-seo-priority
---

# Transparency controls in Dynamics 365 Business Central

Many sovereign compliance policies require transparency, auditing, and governance. These policies give visibility into and record access to data resources, especially changes made to data in the environment.

## Auditing and monitoring 

### Purview

Dynamics 365 Business Central environments automatically emit auditable events to [Microsoft Purview auditing solutions](/purview/audit-solutions-overview). Microsoft Purview auditing solutions give you an integrated way to respond to security events, forensic investigations, internal investigations, and compliance obligations. For Business Central, this process means that the create, update, and delete events that need admin privileges are emitted to Purview's unified audit log. This process helps with security, legal, and compliance investigations across all Microsoft services in your organization. For more information, see [Auditing events in Microsoft Purview](/dynamics365/business-central/dev-itpro/auditing/audit-events-in-purview).


### Change log

The change log lets you track all direct changes a user makes to data in the database. You choose each table and field you want the system to log, and then activate the change log. The change log tracks changes made to data in the tables you select. For more information, see [Auditing changes](/dynamics365/business-central/across-log-changes).

### Field monitoring

Keeping sensitive data secure and private is important for most businesses. To add a layer of security, monitor important fields and get an email when someone changes a value. For example, you might want to get a notification if someone changes your company's IBAN number. For more information, see [Auditing changes](/dynamics365/business-central/across-log-changes).

## Managing access by Microsoft engineers

Sometimes, a Microsoft engineer needs to access a customer's Dynamics 365 Business Central resources, usually because of a support request or an operational failure. To reduce the risk of accidental or intentional data access, Microsoft uses strict controls and logs all access.

### Just-in-time access controls

Most operations, support, and troubleshooting by Microsoft personnel, including subprocessors, don't require access to customer data. If an engineer needs to access production or customer resources, they submit a request through Microsoft's internal Just-In-Time (JIT) access authorization control system. The request needs a justification and internal approval. JIT creates a record of the request. If an authorized JIT approver grants the request, the engineer gets access to the designated resource for a limited time.

### Customer Lockbox

Use [Customer Lockbox for Dynamics 365 Business Central](/dynamics365/business-central/dev-itpro/security/security-online#customer-lockbox) to control whether an engineer can access your customer data in response to a support ticket you start or a problem Microsoft identifies. When Lockbox is on, admins get notified of JIT requests after Microsoft approves them and decide whether to permit or deny access. If you deny access, the engineer's request is rejected and they don't get access to your resources.

All updates to access requests are logged and auditable. For more information, see [Audit lockbox requests - Power Platform | Microsoft Learn](/power-platform/admin/about-lockbox#audit-lockbox-requests).


