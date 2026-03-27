---
title: "Sovereign Controls in Microsoft 365"
description: "Learn how Sovereign controls work in Microsoft 365."
author: lavanyapg
ms.service: microsoft-cloud-sovereignty
ms.topic: overview
ms.date: 10/07/2025
ms.author: kerabun
ms.reviewer: lsuresh
ms.collection: 
    - microsoftcloud-sovereignty
    - microsoftcloud-seo-priority
---

# Sovereign controls in Microsoft 365

Microsoft 365 offers a suite of technical controls that empower organizations, especially in regulated sectors, to meet sovereignty, privacy, and compliance requirements without compromising productivity or innovation. These controls span encryption, data residency, access governance, and operational transparency. They form the backbone of Microsoft’s sovereign cloud strategy.

## Customer-managed encryption keys (CMKs)

Microsoft 365 supports Customer Key through Microsoft Purview, allowing organizations to bring and manage their own encryption keys for data at rest. This feature ensures that sensitive content in Exchange Online, SharePoint Online, and OneDrive is encrypted under customer control.

While you can implement customer managed keys, to guarantee service availability in Microsoft 365, it also implements an availability key. The main purpose of the availability key is to support recovery from unexpected loss of the root keys that you manage.

For more information, see [Customer key overview](/microsoft-365/compliance/customer-key-overview).

## External key management

By using keys stored in a managed HSM, Purview encrypts sensitive classifications, governance policies, and insights with keys that stay within a controlled boundary. This approach enables organizations to comply with data residency and regulatory requirements while using the advanced discovery and governance capabilities of Purview across Microsoft 365 workloads.

By using External Key Management, customers integrate Microsoft 365 with keys stored in their own hardware security modules (HSMs), either on-premises or with trusted hosting providers. This feature adds another layer of encryption control but also adds operational responsibilities.

For more information, see [Service encryption with Microsoft Purview Customer Key](/purview/customer-key-overview) and [Availability key for Customer Key](/purview/customer-key-availability-key-understand).

## Advanced Data Residency

Advanced Data Residency ensures that core Microsoft 365 services store and process customer data within specified geographic boundaries. Advanced Data Residency is critical for organizations operating under strict data localization laws.

For more information, see [Advanced Data Residency in Microsoft 365](/microsoft-365/enterprise/advanced-data-residency).

## Rights Management

Rights Management in Microsoft 365, backed by Azure Rights Management (Azure RMS) and Azure Information Protection, delivers persistent protection that travels with files and emails wherever they go. By pairing classification and sensitivity labels with encryption and granular usage rights (such as view-only, block copy/print, time-bound access, or revocation), organizations retain control of sensitive information beyond traditional network or storage boundaries. Enforcement is identity-centric through Microsoft Entra ID, ensuring only authenticated, authorized principals receive the rights granted by policy.

These capabilities integrate with Microsoft Purview for automated labeling, Data Loss Prevention (DLP), auditing, and insider risk detection, creating an end‑to‑end governance and compliance layer aligned to sovereignty objectives. Conditional access, continuous monitoring, and the ability to revoke or adjust access post‑distribution strengthen operational assurance. Together, Rights Management helps maintain digital sovereignty by ensuring protected content remains governed, compliant, and intentionally usable - internally or with trusted external collaborators.

For more information, see [Microsoft 365 Rights Management](microsoft-365-rights-management.md).

## See also

- [Customer managed keys](/microsoft-365/compliance/customer-key-overview)
- [EU data boundary](/privacy/eudb/eu-data-boundary-learn)

