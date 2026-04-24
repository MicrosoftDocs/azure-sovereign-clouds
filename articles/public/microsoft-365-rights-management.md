---
title: "Rights Management in Microsoft 365"
description: "Learn how Rights Management can be used as a sovereign control."
author: lavanyapg
ms.topic: overview
ms.date: 10/07/2025
ms.author: kerabun
ms.reviewer: lsuresh
ms.collection: 
    - microsoftcloud-sovereignty
    - microsoftcloud-seo-priority
---

# Microsoft 365 Rights Management

In modern enterprises, controlling how sensitive information is used and shared is essential. Microsoft 365 provides Rights Management capabilities that act as a digital sovereignty control. Microsoft 365 Rights Management, often referred to as Azure Rights Management (Azure RMS), allows organizations to enforce protection policies on data regardless of where it travels - whether stored in the cloud, sent via email, or shared externally. By applying persistent protection, organizations maintain control over sensitive content, ensuring that only authorized users can access it and only in ways permitted by organizational policy.

## How it works

At the heart of the Microsoft 365 protection framework is Azure Information Protection (AIP). AIP allows organizations to classify and label content according to its sensitivity. Users can apply labels manually, system rules can apply them automatically, or they can be recommended based on content inspection. After data is labeled, you can protect it with encryption, usage restrictions, and even expiration or revocation policies. Labeling ensures that sensitive files remain secure, even if they leave the organization's controlled environment.

Microsoft 365 Rights Management acts as the enforcement engine behind this protection. It integrates seamlessly across Office applications, SharePoint, OneDrive, and Exchange, and it ensures that protection travels with the data. When a user receives a protected file or email, Microsoft 365 Rights Management verifies their identity through Microsoft Entra ID and enforces the appropriate rights, such as view-only, edit, or print permissions. These controls apply not only to internal users but also to external collaborators, maintaining organizational sovereignty over critical information.

## Integration with compliance and governance

Microsoft 365 Rights Management is closely integrated with Microsoft Purview to provide comprehensive governance and compliance. Purview allows organizations to discover sensitive information across their environment and apply automated protection policies. This integration extends to Data Loss Prevention (DLP) solutions, which can enforce rules at the endpoint, in email, and in the cloud. Organizations can combine labeling, encryption, and monitoring to enforce sovereignty controls, prevent accidental leaks, and meet regulatory requirements.

## Implementation steps

To implement Rights Management effectively, organizations should begin by identifying and classifying their most sensitive data. After the data is classified, you can apply labels and policies to enforce protection automatically. By enabling Microsoft 365 Rights Management, you ensure that these protections are active across all relevant services. Conditional access policies can further refine enforcement by restricting access based on user identity, device, or location. Finally, auditing and monitoring through Purview allows organizations to verify that policies are being followed and to detect any unusual or unauthorized access attempts.

This approach ensures that sensitive data is protected end-to-end, access can be revoked at any time, and organizational sovereignty over information is maintained. Even when data is shared externally, the protection and usage restrictions remain intact, creating a persistent enforcement mechanism.

## Sovereignty alignment

Microsoft 365 Rights Management provides a robust framework for enforcing organizational sovereignty over sensitive information. By integrating classification, labeling, encryption, and monitoring, organizations can control how their data is used, shared, and protected. This persistent protection not only safeguards data from unauthorized access but also helps organizations meet compliance obligations and regulatory requirements, making it a critical component of any enterprise information protection strategy.

## See also

- [Microsoft Information Protection Overview](/microsoft-365/compliance/information-protection)  
- [Azure Information Protection](/azure/information-protection/)  
- [Microsoft Purview Data Loss Prevention](/microsoft-365/compliance/data-loss-prevention-policies)  
