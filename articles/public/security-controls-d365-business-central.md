---
title: Security controls in Dynamics 365 Business Central
description: Learn more about the security controls in Dynamics 365 Business Central
author: jobulsin
ms.topic: overview
ms.date: 07/30/2025
ms.author: jobulsin
ms.reviewer: lsuresh
ms.collection: 
    - microsoftcloud-sovereignty
    - microsoftcloud-seo-priority
---

# Security controls in Dynamics 365 Business Central

Dynamics 365 Business Central runs on Azure as a multitenant service. The same physical hardware stores multiple customers' deployments, virtual machines, and data. Azure uses logical controls to provide the scale and economic benefits of multitenant services and prevents customers from accessing each other's data. **Data Handling and Encryption** controls make sure customer data in Business Central environments stays in its original source.

Dynamics 365 Business Central uses **Azure SQL Database** for data persistence. Azure SQL Database encrypts customer data by using **Transparent Data Encryption (TDE)** technology. All persisted data is encrypted by default with **Microsoft-managed keys**, but Dynamics 365 Business Central customers can encrypt their environment database with customer-managed encryption keys (CMK).

For an overview of security controls that help you protect your data and prevent unauthorized access to Dynamics 365 Business Central, see [aka.ms/bcsecurity](/dynamics365/business-central/dev-itpro/security/security-and-protection).

## Tenant data isolation

Each Dynamics 365 Business Central environment stores data in a dedicated, isolated database. Customer data stays separate and isn't combined with data from other tenants. This isolation is a key security and sovereignty feature that keeps an organization's data separate and protected from others.

## Encryption and key management

Dynamics 365 Business Central encrypts data at rest in real time by using SQL Server Transparent Data Encryption (TDE) with strong keys that Microsoft manages. Power Platform *managed environment* customers with the right licenses and subscriptions can use [customer-managed Keys](/dynamics365/business-central/dev-itpro/security/security-online#customer-managed-encryption-key) to encrypt data in their Dynamics 365 Business Central environment database with their own key. They can:
- Rotate encryption keys on demand for compliance or security policy reasons.
- If needed, revoke Microsoft’s access to the key to instantly make the Dynamics 365 Business Central database undecipherable by Microsoft’s service. This option gives them more control - if they revoke their key, Microsoft (the operator) can't access their data. To revoke Microsoft's access to encryption keys, use [Azure Managed HSM (mHSM)](/azure/key-vault/managed-hsm/overview).

All network communication involving Dynamics 365 Business Central uses industry-standard encryption in transit. The encryption ensures that data sent between the client, the service, and any integrated services can't be intercepted or read by unauthorized parties.

## Service integration security

Dynamics 365 Business Central can integrate with other services, such as Microsoft 365, Power Platform, and various third-party applications. When integrating with Dynamics 365 Business Central, you can set up a firewall or network security group to limit traffic to and from Business Central by using the [*Dynamics365BusinessCentral* service tag](/dynamics365/business-central/dev-itpro/security/security-service-tags). The service tag defines the IP address range used by Dynamics 365 Business Central services and is updated automatically when those IP addresses change.

## Operational security, monitoring, and auditing

The cloud infrastructure that supports Dynamics 365 Business Central adheres to rigorous security practices, including just-in-time access for engineers, extensive monitoring, and audits. All access by Microsoft personnel to customer data is logged and monitored. For instance, anytime a Microsoft engineer elevates access to the production environment, the action is tracked in a tamper-evident system. This option gives assurance that any operator access is tightly controlled.

[Datacenter security](/azure/security/fundamentals/physical-security) describes how Azure regional data centers physically protect your data from external and internal threats.


## See also

* [Data sovereignty in Dynamics 365 Business Central](sovereign-controls-d365-business-central.md)</br>

* [Access controls for Dynamics 365 Business Central](access-controls-d365-business-central.md) 