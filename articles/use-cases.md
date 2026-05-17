---
title: "Sovereign Private Cloud Use Cases and Configurations"
description: "Understand Sovereign Private Cloud use cases and configurations."
author: ronmiab
ms.subservice: sovereign-public-clouds
ms.topic: overview
ms.date: 05/17/2026
ms.author: robess
ms.collection: 
    - microsoftcloud-sovereignty
    - microsoftcloud-seo-priority
---

# Sovereign private cloud use cases and configurations

This article describes common ways organizations use Microsoft Sovereign Private Cloud and the product configurations to build each solution. Each scenario includes a reference architecture diagram as a starting point for your design. 

If you're new to Sovereign Private Cloud, start with [What is Sovereign Private Cloud?](./private/overview/sovereign-private-cloud.md) and [Azure Local overview](./private/azure-local/azure-local-overview.md) before reading this article. 

## Who this article is for

This article is written for anyone evaluating whether Sovereign Private Cloud fits a specific need, including: 

- **Architects** designing a sovereign on-premises or edge environment 

- **IT decision-makers** comparing sovereign options across public cloud, private cloud, and partner clouds 

- **Operators** planning a deployment and choosing the right scale and connectivity model 

- **Compliance, security, and risk leaders** mapping sovereignty requirements to a technical solution 

You don't need deep familiarity with Sovereign Private Cloud to read this article. Each scenario explains what you can do, who it's for, and how to build it before pointing you to the relevant product and reference documentation. 

## What's in scope (and what's not) 

Sovereign Private Cloud is built on Azure Local and can include Microsoft 365 Local, Foundry Local, Azure Virtual Desktop on Azure Local, your own VM and container-based applications, and more. The scenarios in this article highlight common combinations of these capabilities. They don't represent the full set of features available in the platform.  

> [!NOTE]
> These scenarios aren't mutually exclusive. Most real-world deployments combine several. For example, a single environment might run productivity ([Scenario 5](#scenario-5-host-productivity-and-collaboration-locally-with-microsoft-365-local)), AI ([Scenario 4](#scenario-4-run-sovereign-ai-workloads-on-premises)), and multi-tenant hosting ([Scenario 2](#scenario-2-support-multiple-tenants)) on the same infrastructure. Use them as building blocks to design what you need. 

## How to use this article 

Use the following table to quickly map your requirements to a scenario. Each row links to the full scenario section, which includes a plain-English description, the recommended Azure Local configuration, the key services involved, and a reference architecture diagram.

### Foundational scenarios

| Scenario | Connectivity Model | Deployment type | Key services |
| == | == | == | == |
| [Baseline case](#scenario-1-run-a-sovereign-private-cloud-for-your-vms-and-container-apps): Run a sovereign private cloud for your VMs and container apps | Connected | Hyperconverged / Disaggregated / Multi-rack | Your VMs and container apps |
| [Baseline case](#scenario-1-run-a-sovereign-private-cloud-for-your-vms-and-container-apps): Run a disconnected sovereign private cloud, with no connection to the public cloud, for your VMs and container apps | Disconnected | Hyperconverged / Disaggregated | Your VMs and container apps |
| [Support multiple tenants](#scenario-2-support-multiple-tenants) | Connected | Hyperconverged / Disaggregated / Multi-rack | Multi-tenancy |
| [Stay resilient: Maintain high availability and extend resilience with site to cloud recovery](#scenario-3-stay-resilient-high-availability-and-site-to-cloud-disaster-recovery) | Connected / Disconnected | Hyperconverged / Disaggregated / Multi-rack | High availability, rack aware clustering, Azure Site Recovery |

### Workload focused scenarios

| Scenario | Connectivity Model | Deployment type | Key services |
| == | == | == | == |
| [Run sovereign AI workloads on-premises](#scenario-4-run-sovereign-ai-workloads-on-premises) | Connected or Disconnected | Hyperconverged / Disaggregated / Multi-rack | Foundry Local, Edge RAG, Azure AI Video Indexer, AKS on Azure Local, GPU-enabled hardware |
| [Host productivity and collaboration tools locally](#scenario-5-host-productivity-and-collaboration-locally-with-microsoft-365-local) | Connected or Disconnected | Small / Medium / Large Scale | Microsoft 365 Local |
| [Deliver secure virtual desktops and apps to a sovereign workforce](#scenario-6-deliver-secure-virtual-desktops-and-apps-to-a-sovereign-workforce) | Connected | Hyperconverged / Disaggregated | Azure Virtual Desktop |

## Scenario 1: Run a sovereign private cloud for your VMs and container apps

**Who this is for:** Architects and IT decision-makers evaluating Sovereign Private Cloud for the first time, or any team whose immediate need is "give me a sovereign place to run my VMs and Kubernetes apps." 

**What you can do**

Sovereign Private Cloud, powered by Azure Local, gives you a cloud-consistent platform for VM and container workloads inside your own datacenter, branch, or edge site with full control over where your data lives and who can access it. Bring your own hardware from a validated catalog, deploy Azure Local, and run general-purpose Windows or Linux VMs and AKS clusters using the same Azure Resource Manager experience as the public cloud. You can run **connected** to Azure for unified management, monitoring, and updates through Azure Arc, or **fully disconnected** when continuous public-cloud connectivity isn't possible or permitted. 

The platform scales with you. You could start with a **single-node cluster** at a small branch or edge site, grow to a **multi-node hyperconverged cluster** in a regional datacenter, and extend to **multi-rack deployments** of up to 128 nodes each. 

**What this translates to**

| Connectivity model | Deployment type |  Key services  |
| == | == | == |
| [Connected](./private/azure-local/connected-operations-overview.md) or [disconnected](./private/azure-local/disconnected-operations-overview.md) | [Hyperconverged](/azure/azure-local/overview/hyperconverged-overview.md), [disaggregated](/azure/azure-local/overview/disaggregated-overview.md), or [multi-rack](/azure/azure-local/multi-rack/multi-rack-overview.md) | [Azure Local VMs](/azure/azure-local/manage/azure-arc-vm-management-overview.md), [AKS on Azure Local](/azure/aks/aksarc/aks-overview.md), [Azure Arc-based management](/azure/azure-local/manage/arc-extension-management.md) |

### Connected diagram

### Disconnected diagram

## Scenario 2: Support multiple tenants 

**Who this is for:** Sovereign hosting providers, national IT agencies operating centralized platforms for multiple ministries or agencies, managed-service providers serving regulated customers, and large enterprises running a shared internal platform for multiple business units.  

**What you can do**

Sovereign Private Cloud lets you run a multi-tenant platform on Azure Local where multiple organizations, agencies, or business units share a common operations model while running on physically isolated hardware. Each tenant gets its own dedicated Azure Local cluster with dedicated compute and optionally shared storage and networking between tenants.  

Think of a national government IT agency that provides cloud‑like infrastructure services to five different ministries. Each ministry runs on its own dedicated Azure Local cluster, sized to its workload, governed by its own RBAC policies and access controls, while a single platform team operates all of them through a shared Azure control plane. This architecture gives every tenant the strongest possible isolation boundary (separate hardware) while still letting the hosting provider manage the estate centrally and consistently. It dramatically lowers operational cost and complexity compared to standing up a fully separate sovereign environment per tenant. 

Because each tenant runs on dedicated hardware, you can right‑size per tenant: a small ministry might run on a single‑node or small hyperconverged cluster, while a larger one runs on a multi-rack deployment. The shared Azure control plane provides centralized monitoring, updates, and lifecycle management across all tenant clusters, while per‑tenant dashboards, RBAC, and policies give each tenant visibility and control over their own workloads.  

This scenario runs connected to Azure, since the shared control plane, centralized operations, and Azure Arc–based management depend on cloud connectivity from the hosting provider's operations environment. Disconnected support is coming soon. 

** What this translates to **

| Connectivity model | Deployment type |  Key services  |
| == | == | == |
| [Connected](./private/azure-local/connected-operations-overview.md) | [Hyperconverged](/azure/azure-local/overview/hyperconverged-overview.md), [disaggregated](/azure/azure-local/overview/disaggregated-overview.md), or [multi-rack](/azure/azure-local/multi-rack/multi-rack-overview.md) - sized per tenant | [Azure Local VMs](/azure/azure-local/manage/azure-arc-vm-management-overview.md), [AKS on Azure Local](/azure/aks/aksarc/aks-overview.md), [Azure Arc-based management](/azure/azure-local/manage/arc-extension-management.md), RBAC, [centralized monitoring](/azure/azure-local/concepts/monitoring-overview.md) and [updates](/azure/azure-local/update/about-updates-23h2.md) via a shared Azure control plane |

## Scenario 3: Stay resilient: high availability and site to cloud disaster recovery 

**Who this is for:** Infrastructure architects, BC/DR planners, risk and compliance officers, and IT decision‑makers responsible for keeping mission‑critical sovereign workloads. 

**What you can do**

Sovereign Private Cloud is resilient by design, with two clearly defined protection scenarios. Within a fault domain, Azure Local provides native capabilities that keep workloads running through component, node, and rack failures. Across regions, Azure Site Recovery extends this protection by replicating workloads to an Azure cloud region, enabling recovery from workload-level failures. Together, these layers provide a flexible resilience strategy that you can apply per workload tier. 

High availability within a fault domain: every multi-node Azure Local cluster includes native HA capabilities.  

Storage high availability: Storage Spaces Direct (S2D) keeps data online by using two-way or three-way mirroring, so drive or server failures don't take storage offline. In SAN-backed deployments, storage remains available independently of any single compute server, with the SAN array providing native redundancy across controllers, disks, and fabric paths. 

Compute high availability: Failover clustering detects node failures within the fault domain and automatically restarts VMs on surviving servers, ensuring no data loss and minimal downtime. 

Operational high availability: VM live migration enables running workloads to move between servers during planned maintenance, so you can update and patch without workload downtime. 

Rack-aware clustering allows nodes to be distributed across two physical racks located in separate rooms or buildings, connected by high-bandwidth, low-latency networking. This design enhances availability and resiliency by ensuring that if one rack experiences a failure, the other rack continues to maintain data integrity and accessibility. Use this pattern when you need to protect against rack-level failure domains. 

Site-to-cloud disaster recovery with Azure Site Recovery: Azure Site Recovery on Azure Local replicates VM workloads from your sovereign environment to an Azure cloud region, providing protection against site-level outages such as fire, flood, or extended power loss. In the event of a disruption, you can fail over workloads to Azure and operate them there until the primary site is restored, after which you can fail back to Azure Local. This approach suits scenarios where primary operations remain sovereign and on-premises, while disaster recovery is implemented in a designated Azure region. 

**What this translates to**

| Connectivity model | Deployment type |  Key services  |
| == | == | == |
| [Connected](./private/azure-local/connected-operations-overview.md) or [disconnected](./private/azure-local/disconnected-operations-overview.md) (**Note:** Azure Site Recovery only runs connected) | [Hyperconverged](/azure/azure-local/overview/hyperconverged-overview.md) (**Note:** Rack aware cluster only works on hyperconverged), [disaggregated](/azure/azure-local/overview/disaggregated-overview.md), or [multi-rack](/azure/azure-local/multi-rack/multi-rack-overview.md) | [Azure Local HA](/azure/azure-local/manage/disaster-recovery-overview.md) (failover clustering, Storage Spaces Direct, or SAN), [Rack Aware Clustering](/azure/azure-local/concepts/rack-aware-cluster-overview.md), [Azure Site Recovery on Azure Local](/azure/azure-local/manage/azure-site-recovery.md) |

## Scenario 4: Run sovereign AI workloads on-premises 

**Who this is for:** Architects, data and AI leads, and decision-makers in regulated industries who want to deploy generative or analytical AI without sending sensitive data to the public cloud.  

**What you can do**

Sovereign Private Cloud lets you build, deploy, and run AI models and AI‑powered applications inside your own controlled environment, while still benefiting from Microsoft's AI ecosystem. By using Foundry Local on Azure Local, you can host models locally for inference, run agentic and retrieval‑augmented generation (RAG) patterns against private data, and process video and unstructured content by using services like Azure AI Video Indexer. All this happens without data, prompts, or model weights leaving your sovereign boundary. 

Flexible model choice: Foundry Local supports a full spectrum of model options, so you can pick what works for each workload: 

- Open‑source models - from the curated Foundry Local catalog (DeepSeek, Qwen, Mistral, Microsoft‑published) packaged as containers and deployed locally for chat, reasoning, summarization, and agentic workflows. 

- Frontier/proprietary models as a service (MaaS) - for qualified customers, access proprietary models (for example, Azure OpenAI) through a Microsoft‑managed, dedicated inferencing endpoint on a secured Azure Local deployment. 

- Bring your own models (BYO) - onboard any model containerized with ONNX or vLLM, including Hugging Face models, fine‑tuned variants, internal LLMs, and traditional ML/predictive models, all on the same unified local inferencing layer. 

Agentic patterns with bring‑your‑own MCP. Beyond inference, Foundry Local supports end‑to‑end agentic RAG: semantic and hybrid retrieval, multi‑step reasoning, and tool‑driven actions, grounded in customer‑owned content. Connect agents to enterprise systems through Model Context Protocol (MCP): use built‑in connectors to Microsoft 365 Local (Exchange Server, SharePoint Server), or bring your own MCP servers to reach line‑of‑business systems, internal APIs, and other data sources while keeping every retrieval, prompt, and tool invocation inside your sovereign boundary. 

The same scenario works connected to Azure for ongoing model and update management, or fully disconnected where AI must run with no public‑cloud reachability. Because AI workloads are GPU‑bound and often storage‑intensive, Azure Local supports 50+ GPU‑capable validated platforms with NVIDIA GPUs. 

**What this translates to**

| Connectivity model | Deployment type |  Key services  |
| == | == | == |
| [Connected](./private/azure-local/connected-operations-overview.md) or [disconnected](./private/azure-local/disconnected-operations-overview.md) | [Hyperconverged](/azure/azure-local/overview/hyperconverged-overview.md), [disaggregated](/azure/azure-local/overview/disaggregated-overview.md), or [multi-rack](/azure/azure-local/multi-rack/multi-rack-overview.md) | [Foundry Local](/azure/azure-sovereign-clouds/private/foundry-local/what-is-foundry-local-on-azure-local?context=%2Fazure%2Fazure-sovereign-clouds%2Fcontext%2Fcontext), [Edge RAG](/azure/azure-arc/edge-rag/overview?context=%2Fazure%2Fazure-sovereign-clouds%2Fcontext%2Fcontext), [Azure AI Video Indexer](/azure/azure-video-indexer/arc/azure-video-indexer-enabled-by-arc-overview?context=%2Fazure%2Fazure-sovereign-clouds%2Fcontext%2Fcontext), [AKS on Azure Local](/azure/aks/aksarc/aks-overview), [GPU-enabled hardware](/azure/azure-local/manage/gpu-preparation) |

### Run AI workloads, connected control plane 

### Run AI workloads, disconnected control plane

### Scenario 5: Host productivity and collaboration locally with Microsoft 365 Local 

**Who this is for:** IT decision-makers, productivity and collaboration leads, and compliance officers in regulated industries, government, or sovereign-data environments who need to keep email, documents, and collaboration data inside their own boundary, including in disconnected locations.  

**What you can do**

Sovereign Private Cloud lets you run Microsoft 365 Local: productivity and collaboration server workloads such as Exchange Server and SharePoint Server on Azure Local, entirely within your own datacenter or sovereign facility. Your users get the familiar Microsoft productivity experience for email, content management, and communications, while your organization keeps full control over where messages and documents live, who can access them, and how the systems are operated.  

This scenario is designed for organizations that have to comply with national data residency rules, sector-specific regulations, or internal policies that prohibit storing collaboration data in the public cloud. Microsoft 365 Local can run connected to Azure for unified management and updates, or fully disconnected for environments where continuous public-cloud reachability isn't permitted.  

Various configurations and hardware specifications are available to support different scales and requirements, including small-scale, mid-scale, and large scale deployments. The overall architecture of Microsoft 365 Local is tailored to each customer’s needs. Customers should work with their authorized Microsoft partner to appropriately size and design their deployment. 

**What this translates to**

| Connectivity model | Deployment type |  Key services  |
| == | == | == |
| [Connected](./private/azure-local/connected-operations-overview.md) or [disconnected](./private/azure-local/disconnected-operations-overview.md) | Small, Medium, or Large Scale | [Microsoft 365 Local](/azure/azure-sovereign-clouds/private/m365-local/microsoft-365-local-overview) (Exchange Server, SharePoint Server) |

## Scenario 6: Deliver secure virtual desktops and apps to a sovereign workforce 

**Who this is for:** End-user computing leads, workplace and IT decision-makers, and security and compliance teams in regulated industries, government, defense, and any organization where a workforce needs access to Windows desktops and apps but the data behind those sessions can't leave a sovereign boundary.  

**What you can do**

Sovereign Private Cloud lets you run Azure Virtual Desktop (AVD) on Azure Local, delivering full Windows desktops and remote apps to your users from infrastructure you own and operate. User session data, profiles, and the apps themselves stay inside your sovereign environment. Only the encrypted display, keyboard, and mouse traffic reaches the endpoint device. Use this pattern when you need to give a distributed workforce (for example, caseworkers, clinicians, classified-program staff, contact-center agents, contractors) access to sensitive systems and data without putting that data on the endpoint or in the public cloud.   

AVD on Azure Local pairs naturally with the rest of your Sovereign Private Cloud workloads. A common pattern is AVD session hosts running alongside Microsoft 365 Local (so users can access sovereign Exchange and SharePoint from their virtual desktop) and alongside line-of-business VMs and AKS apps (so users can reach internal applications), all on the same hyperconverged cluster or a dedicated AVD cluster when you need to isolate the end user computing (EUC) tier.  

**What this translates to**

| Connectivity model | Deployment type |  Key services  |
| == | == | == |
| [Connected](./private/azure-local/connected-operations-overview.md) | [Hyperconverged](/azure/azure-local/overview/hyperconverged-overview.md), [disaggregated](/azure/azure-local/overview/disaggregated-overview.md) | [Microsoft 365 Local](/azure/azure-sovereign-clouds/private/m365-local/microsoft-365-local-overview) (Exchange Server, SharePoint Server) | [Azure Virtual Desktop on Azure Local](/azure/virtual-desktop/azure-local-overview.md), [Azure Local VMs](/azure/azure-local/manage/azure-arc-vm-management-overview.md), [AKS on Azure Local](/azure/aks/aksarc/aks-overview.md), optional Microsoft 365 Local for in-session productivity |

## Scenario x: Keep developer tools and source code on-premises with GitHub Enterprise Local 

**Who this is for:** Engineering leaders, DevSecOps managers, and security and compliance teams in defense, government, regulated industries, and ISVs serving sovereign customers who need their entire software development lifecycle - source code, identities, build pipelines, secrets - to run inside a sovereign boundary.   

**What you can do**

Sovereign Private Cloud lets you run GitHub Enterprise Local on Azure Local - bringing GitHub's source control, code review, CI/CD, and DevSecOps capabilities entirely on-premises with no dependency on public cloud connectivity. Your engineering teams keep the modern GitHub experience they know - pull requests, Actions, packages, identity integration - while your code, build artifacts, secrets, and audit trails never leave your sovereign environment.  

This scenario applies when intellectual property, classified code, or regulated software supply chains can't be hosted on shared public infrastructure: defense and intelligence software, critical infrastructure control systems, regulated financial services trading code, sovereign-AI model assets, and ISV products that must be developed for sovereign customers under their own data-handling rules. Like the rest of Sovereign Private Cloud, GitHub Enterprise Local can run connected for centralized management or fully disconnected for air-gapped engineering environments.    

**What this translates to**

| Connectivity model | Deployment type |  Key services  |
| == | == | == |
| [Connected](./private/azure-local/connected-operations-overview.md) or [disconnected](./private/azure-local/disconnected-operations-overview.md) | [Hyperconverged](/azure/azure-local/overview/hyperconverged-overview.md) (multi-node) | [Github Enterprise Local](https://learn.github.com/learning-pathways/github-enterprise) (**Note:** GitHub Enterprise Local on Azure Local is currently in private preview), [Azure Local VMs](/azure/azure-local/manage/azure-arc-vm-management-overview.md), [AKS on Azure Local](/azure/aks/aksarc/aks-overview.md) for runners/build agents |

## Scenario x: Confidential Compute and GPU

## Scenario x: Switch between connected and disconnected modes

## Scenario x: Develop in a cloud sandbox and move to the edge
