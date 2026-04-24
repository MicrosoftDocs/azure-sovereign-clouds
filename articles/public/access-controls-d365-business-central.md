---
title: Access controls for Dynamics 365 Business Central
description: Access controls for Dynamics 365 Business Central 
author: jobulsin
ms.topic: overview
ms.date: 07/29/2025
ms.author: jobulsin
ms.reviewer: lsuresh
ms.collection: 
    - microsoftcloud-sovereignty
---

# Access controls for Dynamics 365 Business Central

Dynamics 365 Business Central provides a layered access control framework designed to support regional sovereignty requirements and safeguard user privacy. It integrates identity management through Microsoft Entra, enabling centralized governance and conditional access enforcement. Role-based security allows administrators to assign permissions based on organizational roles and responsibilities.

## Role-Based Access Control (RBAC)

**Role-Based Access Control (RBAC)** is a way to give permissions to users based on their role in your organization. It helps you control access in a simple, manageable way and reduces errors that can happen when you assign permissions individually.

### Environment access

Access to Dynamics 365 Business Central environments is controlled using Microsoft Entra security groups. Admins assign a security group to an environment to decide which users can access it. Users without a Dynamics 365 Business Central license can't access environments, even if they're in the assigned security group. For more information, see [Manage Access to Environments](/dynamics365/business-central/dev-itpro/administration/tenant-admin-center-manage-access).

### User permissions

For users who can access an environment, RBAC uses license configurations, permission sets, and user groups. You can fine-tune what each user or group can do - like reading, writing, or deleting specific data tables or performing certain actions. For example, a user might only view financial records for a department. This granular permission model helps organizations enforce least privilege, so users see only the data they're allowed.

### License entitlements

In Dynamics 365 Business Central, *license entitlements* are the broad filter for what a user can do, while *permission sets* are the fine-grained filters for what the user is allowed to do. For example, even if an admin gives a Team Member-licensed user the "SUPER" permission set (which normally grants full access), the user is still limited by the Team Member license entitlements. Each license type has a set of default permissions, which you can change. For more details, see [License configurations](/dynamics365/business-central/ui-how-users-permissions#licensespermissions).

### Permission sets

A [permission set](/dynamics365/business-central/dev-itpro/developer/devenv-entitlements-and-permissionsets-overview) is a group of permissions to specific objects in Business Central (tables, pages, reports, codeunits, and more). For example, a permission set might grant "Read" access to the Customer table, "Modify" on Sales Orders, and "Execute" on the posting codeunit. 

Microsoft provides many out-of-the-box permission sets for common job roles or tasks. Admins can also create custom permission sets or copy and adjust the defaults to fit their organization's need.

Admins give users access by assigning one or more permission sets to each user, either directly on the user's record or through a security group.

Using multiple smaller permission sets supports least privilege. Instead of one large "accountant" permission, you might have specific sets (read-only, edit, post) and give a user only what they need. If a user has multiple permission sets (from direct assignment and groups), their effective permissions are the union of all granted permissions, still limited by license entitlements.

### Effective permissions

Dynamics 365 Business Central has tools like the Effective Permissions page, where admins can check exactly what a user can do and why. It shows which permission set or group grants each permission, and any security filters applied. These tools help you see the combined effect of license, permission sets, and groups. Changes to license configurations, permission sets, user groups, and users are auditable in [Microsoft Purview](/dynamics365/business-central/dev-itpro/auditing/audit-events-in-purview).

## Privileged Identity Management (PIM)

PIM is a service in Microsoft Entra ID that helps you manage, control, and monitor access to important resources. The features of PIM that help reduce the risk of a malicious insider accessing your data in the cloud are:

- **Just-in-time access**: PIM gives users just-in-time privileged access to Microsoft Entra ID and Azure resources. Users get temporary permissions to do privileged tasks, which prevents malicious or unauthorized users from getting access after the permissions expire.

- **Time-bound access**: Set time-bound access to resources using start and end dates. This access limits how long a user can use sensitive data, reducing exposure risk.

- **Approval-based role activation**: PIM requires approval to activate privileged roles. This step adds control and transparency by making sure a higher authority approves the activation of roles.

- **Multifactor authentication**: PIM enforces multifactor authentication to activate any role. This process asks the user to verify their identity with at least two forms of verification.

- **Access reviews**: Use PIM to run access reviews to check if users still need assigned roles. The reviews help you remove unnecessary access rights and reduce the risk of insider threats.

- **Conditional access**: PIM lets you manage access levels for individual users after they sign in to Microsoft Entra. Microsoft Entra Conditional Access lets you set conditions for signing in to Dynamics 365 Business Central. For example, conditional access lets you limit sign-ins to trusted devices, locations, and acceptable risk levels. 

For Dynamics 365 Business Central, use PIM to:

- [Control role assignment](/entra/id-governance/privileged-identity-management/pim-how-to-add-role-to-user) for the [Dynamics 365 Business Central Administrator Microsoft Entra role](/entra/identity/role-based-access-control/permissions-reference#dynamics-365-business-central-administrator).
- [Control security group memberships](/entra/id-governance/privileged-identity-management/groups-assign-member-owner) that:
  - [Grant access to an environment](/dynamics365/business-central/dev-itpro/administration/tenant-admin-center-manage-access).
  - [Grant permissions in an environment](/dynamics365/business-central/ui-security-groups) to individual users.
- [Set Conditional access](/entra/identity/conditional-access/overview) to set conditions for signing in to Dynamics 365 Business Central. To create a Conditional Access policy for Dynamics 365 Business Central, select **Dynamics 365 Business Central** as **Target Resource** in your Conditional Access policy.

For more information on PIM, see [What is Privileged Identity Management?](/entra/id-governance/privileged-identity-management/pim-configure)

## Granular delegated admin permissions (GDAP)

Partners typically implement, administer, and maintain Dynamics 365 Business Central. By setting up a granular delegated admin permissions (GDAP) relationship between their own tenant and the customer tenant, partners get granular and time-bound access to customer environments. The least-privileged role that enables partners to access and administer Dynamics 365 Business Central environments is the [Dynamics 365 Business Central Administrator role](/entra/identity/role-based-access-control/permissions-reference#dynamics-365-business-central-administrator). For more information, see [Introduction to GDAP](/partner-center/customers/gdap-introduction).

Use the **Delegated BC Admin agent - Partner** [license configuration](/dynamics365/business-central/ui-how-users-permissions#licensespermissions) to set default permissions for partner users with a delegated Dynamics 365 Business Central Administrator role as part of a GDAP relationship in environments. If you're working with multiple partners and need to control which partners can access which environments, see [Manage access to environments](/dynamics365/business-central/dev-itpro/administration/tenant-admin-center-manage-access#manage-access-for-delegated-administrators-and-multitenant-applications).

## See also

* [Data sovereignty in Dynamics 365 Business Central](sovereign-controls-d365-business-central.md)
* [Security controls in Dynamics 365 Business Central](security-controls-d365-business-central.md)
