---
title: Connected Operations for Azure Local
description: Learn about connected operations for Azure Local, a deployment model that enables periodic connectivity to Azure while keeping workloads and data on‑premises, using Azure-based management, governance, and lifecycle services.
author: ronmiab
ms.topic: overview
ms.date: 04/29/2026
ms.author: robess
ms.service: azure
ms.subservice: sovereign-private-clouds
---

# Connected operations for Azure Local

This article provides an overview of connected operations for Azure Local.

Connected operations enable you to run Azure Local with periodic connectivity to Azure while keeping workloads and data on-premises. This deployment model is designed for organizations that want a broad set of Azure Local capabilities on their private cloud, while using Azure-based management and operational services.

By using connected operations, systems connect to Azure periodically to support services such as monitoring, governance, and lifecycle management, while maintaining local control over infrastructure and data.

Industries such as retail, healthcare, manufacturing, and financial services commonly use connected operations, where centralized visibility and compliance are important alongside on-premises data residency.

## What "connected" means in Azure Local

Connected operations provide a balance between on-premises control and cloud-based operational efficiency, while allowing for temporary or intermittent loss of connectivity.

In a connected Azure Local deployment, the system maintains periodic connectivity to Azure for control plane activities. The system supports intermittent disconnections up to 30 days without affecting running workloads. Azure Local is designed to tolerate short periods of disconnection, such as temporary ISP outages, while workloads continue to run on-premises.

In a connected deployment, you can:

- Run hardware on-premises while keeping workloads and data local.

- Manage Azure Local through the Azure portal as a single control plane for deployment and operations.

- Deploy clusters, add nodes, and scale resources using Azure portal workflows.

- Initiate and coordinate cluster lifecycle actions from the Azure portal, with Azure services validating and orchestrating changes.

- Apply identity, access control, and governance using Azure services and the Azure portal.

- Monitor and manage the environment using Azure services when connectivity is available.

- Drive capacity and scaling through cloud-based, policy-governed controls to ensure consistent configuration across sites.

- Update the platform through Azure using Microsoft-validated platform software and supported Original Equipment Manufacturer (OEM) components.

## Deployment options

Connected operations support Azure Local deployment options that scale from single-node systems to large, clustered environments. You can start small and grow to hundreds of nodes as capacity, performance, or availability requirements increase.

### Hyperconverged deployments

Hyperconverged deployments combine compute, storage, and networking on the same nodes to provide a simple, scalable architecture. They’re a good fit for general‑purpose workloads that prioritize simplicity and ease of scaling.

Hyperconverged deployments:

- Support single-node and multi-node clusters, scaling up to 16 nodes on a single rack.

- Use local storage on each node, with optional support for storage area network (SAN) integration in supported configurations.

- Typically use high-speed Ethernet networking, with options for converged or dedicated networks for storage, management, and virtual machine (VM) traffic.

- Support adding GPU-enabled nodes for AI, graphics, and compute-intensive workloads.

For more information, see [What are hyperconverged deployments of Azure Local?](/azure/azure-local/overview/hyperconverged-overview)

### Disaggregated deployments

Disaggregated deployments of Azure Local separate compute and storage into dedicated resources. This separation enables flexible scaling and performance optimization across diverse infrastructures. These deployments support configurations ranging from a single node to up to 64 nodes with SAN-based storage.

Disaggregated deployments use a unified Azure control plane to enable consistent cloud operations and accelerate modern workloads across cloud and edge environments.

Disaggregated deployments:

- Scale compute and storage to match workload needs. Grow each independently based on application and data demand.

- Use local storage on compute nodes with integration to SAN systems. Support both simple and high-capacity deployments.

- Optimize performance by isolating storage on dedicated infrastructure. Improve consistency for performance-sensitive workloads.

- Support data-intensive and modern workloads, including AI and analytics, with flexible, high-capacity storage architectures.

- Integrate validated partner hardware and existing storage environments, extending current investments.

- Provide unified management through the Azure control plane, using consistent tools, governance, and automation.

For more information, see [What are disaggregated deployments of Azure Local?](/azure/azure-local/overview/disaggregated-overview)

### Multi-rack deployments

Multi-rack deployments scale Azure Local across multiple racks to support environments that require higher capacity, performance, and tenant isolation.

Multi-rack deployments:

- Support larger-scale clusters that span multiple racks within a datacenter, scaling up to 128 nodes.

- Increase scale and resiliency through rack-aware designs and fault domain isolation.

- Use SAN-based storage, where an external SAN provides storage and enables independent scaling of compute and storage.

- Separate management, storage, and workload traffic by using dedicated or logically isolated networks.

- Support GPU-enabled nodes for AI, graphics, and other accelerated workloads.

- Enable scale-out, rack-aware networking designs, including:

  - Dedicated storage networks for SAN or disaggregated storage configurations.

  - High-speed east-west fabrics optimized for traffic between racks.

  - Dedicated or logically isolated networks for management, storage, and workload traffic.
  
For more information, see [What are multi-rack deployments of Azure Local?](/azure/azure-local/multi-rack/multi-rack-overview)

## Workloads and services

Azure Local deployments support a broad set of Azure Local infrastructure workloads and enable scenarios that benefit from centralized management. The following list identifies supported services available when Azure Local operates in a connected environment, including management capabilities, workloads, and platform services available with Azure connectivity.

### Management and security

Azure-based management experiences enable you to monitor, secure, govern, and operate Azure Local environments.

The following table provides an overview of management and security services available in connected Azure Local environments:

| Area | Service | Description |
|------|--------|-------------|
| Monitoring | Azure Monitor | Collect and analyze metrics to monitor the health, performance, and availability of on-premises resources. |
| Backup and recovery | Backup | Protect workloads by backing up and restoring Azure Local virtual machines using policy-based recovery. |
| AI assistance | Copilot | Use AI assistance to understand, operate, and troubleshoot environments. |
| Security | Defender | Improve security with built-in protection and threat detection across cloud and on-premises environments. |
| Identity | Entra ID | Manage identities, authentication, and authorization with centralized access control. |
| Secrets management | Key Vault | Store and manage secrets, keys, and certificates securely. |
| Networking | Logical Network | Define and manage logical network configurations for traffic flow and isolation. |
| Identity | Managed Identities | Provide applications with secure access to resources without storing credentials. |
| Governance | Policy | Define and enforce governance rules for consistent and compliant deployments. |
| Tooling | Portal and CLI | Manage resources using graphical or command-line tools. |
| Security | Sentinel | Collect and analyze security data to detect threats and respond to incidents. |
| Backup and recovery | Site Recovery | Replicate workloads and enable failover for disaster recovery. |
| Operations | Update Manager | Coordinate updates to keep systems secure, compliant, and up to date. |

### Workloads and applications

Infrastructure and application workloads run on Azure Local, supporting virtual machines, containers, productivity services, and edge scenarios.

The following table provides an overview of workloads and application services supported by Azure Local:

| Category | Service | Description |
|----------|--------|-------------|
| Containers | Container Apps | Run containerized applications with managed scaling without managing Kubernetes infrastructure. |
| Storage | External SAN | Use SAN to provide shared storage and scale compute and storage independently. |
| Edge | IoT Operations | Collect and process data from connected devices for edge scenarios. |
| Containers | Kubernetes | Orchestrate and manage containerized workloads. |
| Applications | Microsoft 365 Local | Run Exchange Server, SharePoint Server, and Skype for Business Server locally. |
| Migration | Migrate | Discover, assess, and prepare workloads for migration to Azure. |
| Virtualization | Virtual Desktop | Provide secure access to virtual desktops and applications. |
| Virtualization | Virtual Machines | Run Windows and Linux virtual machines for enterprise workloads. |

### Data and AI

Data services and AI capabilities enable analytics, search, and AI‑powered applications using local and connected data.

The following table provides an overview of data and AI services available for Azure Local:

| Category | Service | Description |
|----------|--------|-------------|
| AI services | AI Content Safety | Detect and filter harmful or inappropriate content. |
| AI services | AI Document Intelligence | Extract text and structured data from documents. |
| AI services | AI Language | Analyze text for summarization, classification, and sentiment. |
| Search | AI Local Search | Index and retrieve data across local and connected sources. |
| AI services | AI Speech | Enable speech-to-text and text-to-speech capabilities. |
| AI services | AI Translator | Translate text and documents across languages. |
| AI services | AI Video Indexer | Analyze video to extract speech, topics, and visual insights. |
| Analytics | Decision Anomaly Detector | Detect anomalies in time series data. |
| AI patterns | Edge RAG | Ground AI outputs using local enterprise data. |
| AI platform | Foundry Local on Azure Local | Build and run AI models locally with Azure AI tools. |
| AI platform | Machine Learning | Train, deploy, and manage machine learning models. |
| Data | SQL | Store and manage relational data. |
| AI services | Vision Container Read OCR | Extract printed and handwritten text from images and documents. |

## Related content

- Learn more about [What is Azure Local?](/azure/azure-local/overview)
- Review [Azure Local FAQs](/azure/azure-local/faq).
- Explore [Disconnected operations for Azure Local](/azure/azure-local/manage/disconnected-operations-overview).
<!--- **GitHub Enterprise Local:** enable modern DevSecOps and keep code, identity, and operations fully on-premises with GitHub Enterprise Local on Azure Local-->
