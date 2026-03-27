---
title: Access controls for Dataverse and Power Platform
description: Access controls for Dataverse and Power Platform 
author: lavanyapg
ms.service: microsoft-cloud-sovereignty
ms.topic: overview
ms.date: 03/24/2025
ms.author: kerabun
ms.reviewer: lsuresh
ms.collection: 
    - microsoftcloud-sovereignty
---

# Access controls for Dataverse and Power Platform

Power Platform provides multiple access controls to respect regional sovereignty regulations and user privacy. **Data Handling and Encryption** controls ensure that customer data in Dataverse remains in its original source (for example, Dataverse or SharePoint).

Power Platform apps use **Azure Storage** and **Azure SQL Database** for data persistence. Data used in mobile apps is encrypted and stored in **SQL Express.** Azure SQL Database fully encrypts customer data by using **Transparent Data Encryption (TDE)** technology. All persisted data is encrypted by default by using **Microsoft-managed keys,** and many Power Platform products let customers manage their own encryption keys (customer-managed keys) in Microsoft Azure Key Vault.

In addition, **Identity Management**, **Role-Based Security**, and **Fine-Grained Permission controls** enable Dataverse and Power Platform customers to combine **business units**, **role-based security**, **row-based security**, and **column-based security**.

These capabilities allow precise control over user access to information to help comply with sovereignty control requirements.

## Role-Based Access Control (RBAC)

**Role-Based Access Control (RBAC)**, also known as **role-based security**, is a method that gives permissions to end-users based on their role in your organization. It helps you manage access in a simple and manageable way. It also reduces errors that can happen when you assign permissions individually.

Fine-grained RBAC controls in Dataverse can ensure that users have precisely the permissions required for their roles. You can grant permissions at the environment, role, database, table, row, and column levels. Organizations can define who can read, write, delete, or modify specific records, fields, or apps. This granularity helps to respect customer data sovereignty. For more information, see [Configure user security in an environment - Power Platform \| Microsoft Learn](/power-platform/admin/database-security).

Dataverse environments come with predefined security roles that follow the principle of *minimum required access*. These roles give users the least access they need to do their tasks within specific apps. The available roles depend on the environment type and installed apps.

If an environment has a Dataverse database, follow the minimum required access principles and minimize the number of users with access to the **System Administrator role**.

For environments without a Dataverse database, two predefined roles exist:

1. **Environment Admin**: Performs administrative actions, prepares databases, manages resources, and creates data loss prevention policies.

1. **Environment Maker**: Creates resources (apps, connections, APIs, and more) but lacks data access privileges.

To control access to both apps and Dataverse through Power Apps, follow the guidance given here [How to control app and Dataverse access - Power Platform Community (microsoft.com)](https://powerusers.microsoft.com/t5/Building-Power-Apps/How-to-control-app-and-Dataverse-access/m-p/2198305#M550635).

## Privileged Identity Management (PIM)

[PIM](/entra/id-governance/privileged-identity-management/pim-configure) is a service in Microsoft Entra ID that helps you manage control and monitor access to important resources. You can use it to protect your sovereign Dataverse data from the risk of access by a malicious insider or a malicious Microsoft Cloud provider. Here are some features of PIM that can help you:

- **Just-In-Time Access**: PIM gives users just-in-time privileged access to Microsoft Entra ID and Azure resources. This access means that users receive temporary permissions to perform privileged tasks, which prevents malicious or unauthorized users from gaining access after the permissions expire.

- **Time-Bound Access**: Set time-bound access to resources by using start and end dates. This type of access limits the time that a user can access sensitive data, reducing exposure risk.  

- **Approval-Based Role Activation**: PIM requires approval to activate privileged roles. This step adds an extra layer of control and transparency by making sure that a higher authority approves the activation of roles.

- **Multi-Factor Authentication**: PIM enforces multifactor authentication to activate any role. This process requests the user to substantiate their identity through a minimum of two separate forms of verification.

- **Access Reviews**: PIM allows you to conduct access reviews to ensure users still need assigned roles. The reviews help you remove unnecessary access rights and reduce the risk of insider threats.

By using Entra's other conditional access and location awareness controls, PIM can help you control access to environments by only allowing trusted devices, locations, and other conditions, which can be evaluated for authentication. Use these features of PIM to reduce the risk of a malicious insider or a compromised Microsoft Cloud provider accessing your data stored in the Dynamics cloud. For more information on PIM, see [What is Privileged Identity Management? - Microsoft Entra ID Governance \| Microsoft Learn](/entra/id-governance/privileged-identity-management/pim-configure).

## Security roles

You can secure your data and ensure that users have the least privilege necessary by using Dataverse authorization and data level security roles that define row, field, hierarchical, and group protection. These roles give you the ability to specify granular, [field-level security](/power-platform/admin/field-level-security). Dataverse implements both [privilege and access checks](/power-platform/admin/how-record-access-determined) to help you maintain this control. Privileges are managed through security roles or team assignments, and access checks are managed through ownership, role access, shared access, or hierarchy access.

For example, to reduce the risk of inadvertent data disclosures and ensure that only authorized personnel can make data transfers, set user permissions to restrict Entra Guest user accounts from making Power Apps. Make sure that when you assign privileges and inheritances to a user or team, each individual only gets the appropriate level of privileges.

More information about [Dataverse security roles and privileges](/power-platform/admin/security-roles-privileges) is available to help you ensure only authorized users can access your sovereign assets.

Currently, you can use the Admin Center to [manage user and permissions management settings](/power-platform/admin/).

- [Dataverse teams](/power-platform/admin/manage-teams): To simplify user management and ensure that privileges and permissions are consistently implemented, we recommend using Microsoft Entra group teams.

- [Assign security roles to users](/power-platform/admin/database-security): Use the Admin Center UI to manage users through creating security roles. 

- [Configure user security in an environment](/power-platform/admin/database-security): In the Admin center, specify security roles within a given environment to restrict which users can do what. Many pre-existing roles are configured to help you streamline this process. Tenant-level policy can be scoped to provide top-level controls that minimize the risk of data loss due to individual environments being misconfigured, by setting the scope to *all environments*. For more information, see [Security concepts in Microsoft Dataverse - Power Platform](/power-platform/admin/wp-security-cds).

## Business units

Every Dataverse database has a single root business unit. This business unit defines a security boundary, which works with role-based security, to manage users and the data they can access. These boundaries can facilitate sovereign controls, especially for large or complex organizations with multiple business units that have different levels of access and restrictions. Creating child business units and providing roles with the minimum necessary access permissions serve as guardrails to protect data sovereignty. [Business Units](/power-platform/admin/create-edit-business-units) are specific to an environment and can be managed through the admin center Environment controls.

Dataverse also uses the controls of Microsoft Entra identity and access management mechanisms to help ensure that only authorized users can access the environment, data, and reports. Also, because Dataverse is built on Azure, it benefits from the Azure platform's powerful security technologies.

## Encryption and key management

Dynamics 365 runs on Azure as a multitenant service. This architecture means that the service stores multiple customers' deployments, virtual machines, and data on the same physical hardware. Azure uses logical controls to provide the scale and economic benefits of multitenant services while preventing customers from accessing each other's data.

Customer data in Dataverse stays in its original source (for example, Dataverse or SharePoint). Power Platform apps use **Azure Storage** and **Azure SQL Database** for data persistence. Data used in mobile apps is encrypted and stored in **SQL Express**.

Dataverse encrypts data on disk in real time by using SQL Server Transparent Data Encryption (TDE) with strong keys that Microsoft manages. Azure Storage Encryption encrypts customer data stored in Azure Blob storage. Power Platform encrypts all data that it saves by default using keys that Microsoft manages. Dynamics *managed environment* customers who have the right licenses and subscriptions should use [Customer-managed Keys](/power-platform/admin/customer-managed-key) when they can. Customer-managed keys work with [Dataverse and most Dynamics 365 apps](/power-platform/admin/customer-managed-key).

> [!CAUTION]
> > If you apply the customer-managed keys to an environment that already has existing Power Automate flows, the flows data continues to be encrypted by using the Microsoft-managed key, *not* by using the customer's key. Also, the customer-managed keys encrypt only data stored in Microsoft Dataverse. Any non-Dataverse data and all connector settings are encrypted by the Microsoft-managed key.*Note that encryption on disk doesn't stop operator access while data is in use.*

For Power BI, Microsoft managed keys encrypt data at rest and in process by default. To better meet sovereign requirements, bring your own key (BYOK) to manage semantic model data uploaded from the Power BI Desktop (.pbix) file. Depending on your specific needs, you can keep your customer-managed keys or BYOK keys in the Azure Key Vault, or in your own on-premises Hardware Security Module (HSM). To give more access control and transparency, Azure Key Vault logs every successful or attempted access. Azure Managed HSM (mHSM) support for Dataverse is in preview. This support lets you revoke Microsoft's access to the keys if you need to.

For more information, see [Manage your customer-managed encryption key in Power Platform - Power Platform \| Microsoft Learn](/power-platform/admin/customer-managed-key).

## See also

* [How role-based security can be used to control access to entities (Developer Guide for Dynamics 365 Customer Engagement (on-premise)) \| Microsoft Learn](/dynamics365/customerengagement/on-premises/developer/security-dev/how-role-based-security-control-access-entities?view=op-9-1&preserve-view=true).
* [Role-based security - Finance & Operations \| Dynamics 365 \| Microsoft Learn](/dynamics365/fin-ops-core/dev-itpro/sysadmin/role-based-security).
* [Manage user settings as a Microsoft Power Platform administrator - Power Platform \| Microsoft Learn](/power-platform/admin/users-settings).
* [Column-level security - Power Platform \| Microsoft Learn](/power-platform/admin/field-level-security)
* [How access to a record is determined - Power Platform \| Microsoft Learn](/power-platform/admin/how-record-access-determined)
* [Protecting data with Dataverse \| Microsoft Power Apps](https://powerapps.microsoft.com/blog/protecting-data-with-dataverse/)

