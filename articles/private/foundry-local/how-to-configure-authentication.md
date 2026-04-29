---
title: "Configure authentication for Foundry Local enabled by Azure Arc"
titleSuffix: Foundry Local on Azure Local
description: "Configure Microsoft Entra ID authentication for your Foundry Local enabled by Azure Arc deployment, including app registration, role creation, user assignment, and Azure RBAC."
ms.service: azure
ms.subservice: sovereign-private-clouds
appliesto:
- Foundry Local enabled by Azure Arc
ms.topic: how-to
ms.author: cwatson
author: cwatson-cat
ms.date: 04/28/2026
ai-usage: ai-assisted
customer intent: As a platform engineer, I want to configure Microsoft Entra ID authentication for Foundry Local so that my team can securely access inference endpoints with identity-based access control.
---

# Configure authentication for Foundry Local enabled by Azure Arc

Configure Microsoft Entra ID authentication for your Foundry Local enabled by Azure Arc deployment. This guide walks you through app registration, role creation, user assignment, and Azure role-based access control (Azure RBAC) configuration so your team can securely access inference endpoints.

You might need to work with your Microsoft Entra or cloud administrator to complete these steps.

[!INCLUDE [foundry-local-preview](includes/foundry-local-preview.md)]

## Prerequisites

Before you begin, make sure you have:

- An active Azure subscription. If you don't have one, [create one](https://azure.microsoft.com/free/) before you begin.
- An Azure Arc-connected Kubernetes cluster with the Foundry Local extension installed. See [Deploy Foundry Local as an Azure Arc extension](deploy-foundry-local-arc-extension.md).
- Microsoft Entra ID permissions:
  - Permissions to create a Microsoft Entra app registration.
  - Ability to add app roles to the application.

## Step 1: Register an application in Microsoft Entra ID

Create an application registration for Foundry Local in your Microsoft Entra ID tenant.

1. In the Azure portal, go to **Microsoft Entra ID**.
1. Go to the appropriate tenant and select **Manage** > **App registrations**.
1. Select **New registration**.

1. Enter a namefor your application, such as FoundryLocal-Production.
1. For **Supported account types**, select **Accounts in this organizational directory only (Single tenant)**.

1. Select **Register**.
1. After registration completes, note the **Application (client) ID** and **Directory (tenant) ID**. You need these values later.

## Step 2: Expose an API

Configure an Application ID URI and add a delegated scope so user tokens include the required claims.

1. In the app registration, select **Manage** > **Expose an API**.
1. Next to **Application ID URI**, select **Set** and accept the default value `api://<client-id>`.

   :::image type="content" source="media/how-to-configure-authentication/entra-expose-api.png" alt-text="Screenshot showing the Expose an API page with the Application ID URI." lightbox="media/how-to-configure-authentication/entra-expose-api.png":::

1. Select **Add a scope**:

   | Field | Value |
   |---|---|
   | Scope name | `foundry_access` |
   | Who can consent | Admins only |
   | Admin consent display name | Access Foundry Local inference endpoints |
   | Admin consent description | Allows the application to access Foundry Local inference endpoints on behalf of the signed-in user |

1. Select **Add scope**.

The delegated scope ensures that user tokens include a `scp` claim, which the authentication sidecar requires. Without a scope, tokens are rejected with `401 invalid_token`.

## Step 3: Set token version to v2.0

Configure the application to issue v2.0 tokens. This step is **critical**. Without it, tokens use the v1.0 format with an issuer (`https://sts.windows.net/`) that the authentication sidecar doesn't accept.

1. In the app registration, select **Manage** > **Manifest**.
1. Find the `"accessTokenAcceptedVersion"` property and change its value from `null` to `2`.
1. Select **Save**.

## Step 4: Create an app role

Create an app role for service principals and managed identities. User tokens get a `scp` claim from the delegated scope (Step 2), but service principal and managed identity tokens need a `roles` claim instead. If you don't assign an app role, their tokens are rejected.

1. In the app registration, select **Manage** > **App roles**.

1. Select **Create app role** and enter the following values:

   | Field | Value |
   |---|---|
   | Display name | `FoundryInferenceAccess` |
   | Allowed member types | Applications |
   | Value | `FoundryInferenceAccess` |
   | Description | Access Foundry Local inference endpoints |
   | Do you want to enable this app role? | Checked |

1. Select **Apply**.

   :::image type="content" source="media/how-to-configure-authentication/entra-app-role-created.png" alt-text="Screenshot showing the app role successfully created." lightbox="media/how-to-configure-authentication/entra-app-role-created.png":::

This app role is a token format requirement. It ensures service principal tokens contain the `roles` claim needed by the authentication sidecar. Access-level permissions are controlled separately through Azure RBAC role assignments (Step 7).

## Step 5: Authorize Azure CLI

Authorize the Azure CLI as a client application so your team can acquire tokens by using `az account get-access-token`.

1. In the app registration, select **Manage** > **Expose an API**.
1. Under **Authorized client applications**, select **Add a client application**.
1. Enter the Azure CLI client ID: `04b07795-8ddb-461a-bbee-02f9e1bf7b46`.
1. Check the scope you created in Step 2.
1. Select **Add application**.

## Grant access to users and applications

After completing the app registration and installing the Foundry Local enabled by Arc cluster, configure Azure RBAC to control who can access Foundry Local endpoints.

## Step 6: Assign Azure RBAC roles to users

Assign an Azure RBAC role to each user or group that needs to access Foundry Local endpoints. The role determines the level of access:

| Role | Access level | Use case |
|---|---|---|
| Cognitive Services OpenAI User | Data plane only (inference calls) | End users calling chat/completions |
| Cognitive Services Contributor | Data plane + control plane (deploy/manage models) | Administrators managing deployments |

You can complete this step from the CLI or Azure portal.

### [CLI](#tab/rbac-cli)

```bash
az role assignment create \
  --assignee "<USER_OR_GROUP_OBJECT_ID>" \
  --role "Cognitive Services OpenAI User" \
  --scope "/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Kubernetes/connectedClusters/<CLUSTER_NAME>"
```

### [Azure portal](#tab/rbac-portal)

1. Go to **Kubernetes Services**.
1. Choose your Arc connected cluster.
1. Select **Access control (IAM)**.
1. Select **Role Assignments**.

1. Select **Add role assignment**.

1. Choose **Cognitive Services OpenAI User**or **Cognitive Services Contributor**.
1. Grant permissions to the relevant users or group.

---

## Step 7: Grant the cluster's Arc identity permission to read role assignments

The cluster's managed identity must be able to query Azure RBAC to verify caller permissions. Without this assignment, all authenticated requests fail with `500 rbac_check_unavailable`.

```bash
# Get the cluster's Arc identity
ARC_PRINCIPAL_ID=$(az connectedk8s show \
  -n <CLUSTER_NAME> \
  -g <RESOURCE_GROUP> \
  --query "identity.principalId" -o tsv)

# Assign the role
az role assignment create \
  --assignee "$ARC_PRINCIPAL_ID" \
  --role "Cognitive Services OpenAI User" \
  --scope "/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Kubernetes/connectedClusters/<CLUSTER_NAME>"
```

This step uses a different identity for each cluster: the Arc-connected cluster's own service principal. Run this command once per cluster.

## (Optional) Grant access to managed identities and service principals

If managed identities or service principals call the API, two additional steps are required. Human users authenticate via delegated scope (Step 2) and don't need these steps.

### Assign the app role to the managed identity

Service principal and managed identity tokens require a `roles` claim (unlike user tokens which get `scp`). Assign the app role created in Step 4:

```bash
# Get the service principal object ID for your managed identity
SP_OBJECT_ID=$(az ad sp show --id <MSI_CLIENT_ID> --query id -o tsv)

# Get the service principal object ID for your app registration
APP_SP_OBJECT_ID=$(az ad sp show --id <YOUR_APP_CLIENT_ID> --query id -o tsv)

# Assign the app role
az rest --method POST \
  --uri "https://graph.microsoft.com/v1.0/servicePrincipals/${SP_OBJECT_ID}/appRoleAssignments" \
  --body "{
    \"principalId\": \"${SP_OBJECT_ID}\",
    \"resourceId\": \"${APP_SP_OBJECT_ID}\",
    \"appRoleId\": \"<APP_ROLE_ID>\"
  }"
```

To find the app role ID, go to **App registrations** > your app > **App roles** and note the ID of the FoundryInferenceAccess role.

### Assign Azure RBAC role to the managed identity

Same as Step 6, but use the managed identity's object ID as the assignee:

```bash
az role assignment create \
  --assignee "<MSI_OBJECT_ID>" \
  --role "Cognitive Services OpenAI User" \
  --scope "/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Kubernetes/connectedClusters/<CLUSTER_NAME>"
```

## Related content

- [Authentication and authorization in Foundry Local enabled by Azure Arc](concept-authentication-authorization.md)
- [Deploy Foundry Local as an Arc extension](deploy-foundry-local-arc-extension.md)
- [Deploy and run your first model on Foundry Local on Azure Local](deploy-run-first-model.md)
