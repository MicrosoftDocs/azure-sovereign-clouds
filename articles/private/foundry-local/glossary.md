---
title: "Glossary for Foundry Local on Azure Local"
titleSuffix: Foundry Local on Azure Local
description: "Definitions of key terms used in Foundry Local on Azure Local documentation."
ms.service: azure
ms.subservice: sovereign-private-clouds
appliesto:
- Foundry Local on Azure Local
ms.topic: reference
ms.author: cwatson
author: cwatson-cat
ms.date: 04/14/2026
ai-usage: ai-assisted
customer intent: As a platform engineer or developer, I want definitions for Foundry Local on Azure Local terminology so that I can understand the documentation and configure the platform correctly.
---

# Glossary for Foundry Local on Azure Local

This article defines key terms used throughout Foundry Local on Azure Local documentation.

[!INCLUDE [foundry-local-preview](includes/foundry-local-preview.md)]

## A

### API key

A secret token used to authenticate to a model's inference endpoint. Each deployed model's endpoint is secured by an API key pair (primary and secondary). Include the API key in your request header to authenticate. The inference operator generates API keys for each deployment, stores them in Kubernetes secrets, and can rotate them. Both keys are valid simultaneously to support zero-downtime rotation.

## B

### Bring Your Own Model (BYO)

A scenario where you provide your own model - rather than using a model from the Foundry catalog - packaged as a container image stored in an OCI-compatible registry. BYO models might be proprietary or open-source models that you obtain and want to run on your own infrastructure. The platform supports BYO models so you're not limited to the curated catalog.

## C

### Catalog

The set of prepackaged models provided by the Foundry catalog that you can deploy through Foundry Local. These models are containerized and stored in a registry. For metadata and availability in your cluster, see [model catalog](#model-catalog).

## F

### Foundry Local

The underlying on-device AI inference runtime that runs models on local hardware. In this context, it refers to the core technology that enables AI model serving outside the cloud. Foundry Local is the engine that Foundry Local on Azure Local uses to run models on your infrastructure.

## G

### Generative model

An AI model that generates content - typically free-form text - in response to prompts. These models produce novel output rather than selecting from predefined categories. Examples include language models used for chat, question answering, and text generation.

## I

### Inference operator

The management layer of Foundry Local on Azure Local. It handles requests to deploy or manage models (through YAML or REST calls) and orchestrates the necessary Kubernetes resources - Deployments, Services, Secrets, and Ingress resources. The inference operator manages model lifecycle but doesn't handle inference computations directly.

## L

### Lazy registration

The automatic creation of a Model CR from the catalog when a ModelDeployment first references it. If the operator doesn't find an existing Model CR for a catalog model, it creates one automatically from the catalog ConfigMap. This process means you don't need to create Model CRs manually for standard catalog models. Lazy registration is enabled by default and can be turned off in the operator configuration.

## M

### Model

A Custom Resource Definition (CRD) that defines a model available for deployment. A Model resource describes where the model comes from (catalog or custom registry), its metadata, and any hardware-specific variants.

### Model catalog

The set of prepackaged models from the Azure AI Foundry catalog that you can deploy through Foundry Local. In your cluster, the catalog is represented as a ConfigMap (`foundry-local-catalog`) that the catalog-sync component keeps up to date with metadata from the Foundry catalog API.

### ModelDeployment

A Custom Resource Definition (CRD) that creates a running inference endpoint. A ModelDeployment specifies which model to run, what hardware to use (CPU or GPU), scaling configuration, resource limits, and optional endpoint exposure through ingress. When you create a ModelDeployment, the inference operator launches the model server pod and wires up authentication and TLS.

## N

### NGINX

A high-performance web server and reverse proxy. In Foundry Local deployments, each ModelDeployment pod includes an NGINX sidecar container that handles TLS termination and API key authentication. NGINX proxies valid, authenticated requests to the model server container. This detail is useful to know when troubleshooting connection or authentication problems.

## O

### ONNX

Open Neural Network Exchange - an open format for AI models. Foundry Local's inference servers use ONNX Runtime, so models need to be in ONNX format (or converted to it) internally. ONNX is supported by many frameworks including PyTorch and TensorFlow. If you bring a custom model, converting it to ONNX is typically the key preparation step.

### Open Web UI

A web-based user interface accessible from a browser that lets you select a deployed model and interact with it. It's not enabled by default and is primarily intended for convenience during development - not as an end-user production application.

### ORAS

OCI Registry As Storage - a protocol for storing and distributing model artifacts in OCI-compatible container registries (such as Azure Container Registry, GitHub Container Registry, or Docker Hub). Foundry Local uses ORAS for BYO predictive model scenarios.

## P

### Predictive model

A traditional machine learning model that produces predictions or classifications from input data. The output is deterministic - a category, a score, or a numeric value - rather than free-form text. Examples include image classifiers, regression models, and anomaly detection models. Foundry Local on Azure Local runs predictive models through ONNX Runtime.

## Related content

- [What is Foundry Local on Azure Local?](what-is-foundry-local-on-azure-local.md)
- [Inference operator and model lifecycle](concept-inference-operator.md)
- [ModelDeployment and operator configuration reference](reference-model-deployment-operator.md)
