---
title: "Configure TLS and authentication for Foundry Local on Azure Local"
titleSuffix: Foundry Local on Azure Local
description: "Configure TLS encryption and API key authentication to secure model inference endpoints on Foundry Local on Azure Local."
ms.service: azure
ms.subservice: sovereign-private-clouds
appliesto:
- Foundry Local on Azure Local
ms.topic: how-to
ms.author: cwatson
author: cwatson-cat
ms.date: 04/14/2026
ai-usage: ai-assisted
customer intent: As a platform engineer, I want to configure TLS encryption and API key authentication for Foundry Local on Azure Local so that I can secure AI inference endpoints in my environment.
---

# Configure TLS and authentication for Foundry Local on Azure Local

Foundry Local on Azure Local encrypts all internal service communication by using TLS. Each model service uses self-signed certificates that the cluster manages. This article explains how the TLS setup works and how to configure secure connections inside the cluster, across namespaces, and through external ingress. It also covers API key authentication, including how to retrieve, use, and rotate keys.

[!INCLUDE [foundry-local-preview](includes/foundry-local-preview.md)]

## Prerequisites

Before you begin, request preview deployment access: [Request deployment access](what-is-foundry-local-on-azure-local.md#request-deployment-access).

Automated certificate management requires [cert-manager](https://cert-manager.io/) and [Trust Manager](https://cert-manager.io/docs/trust/trust-manager/) installed on your cluster:

- **cert-manager** issues a self-signed root CA and per-service certificates.
- **trust-manager** distributes the root CA certificate as a trust bundle to all namespaces so other pods can trust the internal certificates.

> [!IMPORTANT]
> The Foundry Local Helm chart doesn't automatically install cert-manager and trust-manager. Install both components before you deploy Foundry Local on Azure Local. 

## How internal TLS works

All traffic between Foundry Local components is encrypted by using TLS. Each service pod runs an NGINX sidecar proxy that:

- Terminates TLS on port 443.
- Forwards requests to the main container over HTTP on localhost (typically port 8001 or 5000).

The main application only listens on localhost, so all external communication must go through the sidecar.

### Root CA and certificate issuance

On first deployment, cert-manager creates a self-signed root Certificate Authority in the Foundry Local namespace and stores it in a Kubernetes secret named `root-ca-secret`. Using this root CA, cert-manager issues TLS certificates for Foundry services through a ClusterIssuer:

```yaml
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: foundry-local-ca-issuer
spec:
  ca:
    secretName: root-ca-secret
    namespace: foundry-local
```

By default, Foundry uses a wildcard certificate (for example, `*.foundry-local.svc.cluster.local`) that covers multiple internal services:

```yaml
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: inference-service-tls
  namespace: foundry-local
spec:
  secretName: inference-service-tls-secret
  commonName: inference-service.foundry-local.svc.cluster.local
  dnsNames:
    - inference-service.foundry-local.svc.cluster.local
    - "*.foundry-local.svc.cluster.local"
  issuerRef:
    name: foundry-local-ca-issuer
    kind: ClusterIssuer
```

cert-manager automatically rotates service certificates before they expire - for example, renewing 30 days before a 90-day expiry. The rotation is seamless: Kubernetes updates the secret and NGINX picks up the new certificate without downtime.

All Foundry sidecars present certificates issued by the internal CA. Because the CA certificate is distributed cluster-wide, every service trusts calls from other services. By default, sidecars use one-way TLS and don't require client certificates for internal calls.

## Configure cross-namespace access

If your application runs in a different Kubernetes namespace and needs to call Foundry inference services, you can do so over TLS using internal DNS names.

trust-manager publishes the root CA to a ConfigMap across all namespaces by default. Create a Bundle resource to distribute it:

```yaml
apiVersion: trust.cert-manager.io/v1alpha1
kind: Bundle
metadata:
  name: foundry-local-ca-bundle
spec:
  sources:
    - secret:
        name: root-ca-secret
        key: ca.crt
  target:
    configMap:
      key: ca-bundle.crt
    namespaceSelector: {}
```

This creation adds a `foundry-local-ca-bundle` ConfigMap in every namespace containing the root CA certificate. Your application can mount this ConfigMap as a file or import it into its trust store.

To call a Foundry service from another namespace, use its internal DNS name, for example `https://inference-service.foundry-local.svc.cluster.local`. Configure your HTTP client to trust the CA by appending `ca-bundle.crt` to your system trust store or setting it explicitly on the client.

When your application makes an HTTPS request, the Foundry service's NGINX sidecar presents a certificate signed by the internal CA. Because your client trusts that CA through the bundle, the TLS handshake succeeds. API key authentication for inference requests is covered in the [Authentication](#authentication) section.

## Configure external access through ingress

For access outside the cluster, use a Kubernetes ingress controller (such as NGINX Ingress) in front of Foundry's services. Foundry Local automatically configures ingress resources with specific NGINX annotations to enable secure communication. Foundry Local doesn't deploy an ingress controller itself - you bring your own.

> [!IMPORTANT]
> Install the ingress controller before you deploy the inference operator.

```bash
# Install ingress-nginx (NodePort)
helm install ingress-nginx ingress-nginx/ingress-nginx \
  --namespace ingress-nginx \
  --create-namespace \
  --set controller.service.type=NodePort \
  --set controller.hostPort.enabled=true \
  --wait
```

To configure external TLS with your own certificate:

```bash
# Step 1: Create a TLS secret from existing certificate files
kubectl create secret tls my-custom-tls \
  --namespace foundry-inference \
  --cert=path/to/tls.crt \
  --key=path/to/tls.key
```

Then reference the secret in your ModelDeployment:

```yaml
# Step 2: Deploy with a custom certificate
apiVersion: foundrylocal.azure.com/v1
kind: InferenceService
metadata:
  name: phi-3-mini
  namespace: foundry-inference
spec:
  name: phi-3-mini
  inferenceType: generative
  hardware: cpu
  modelSource:
    foundry:
      modelName: Phi-3-mini-128k-instruct-generic-cpu:2
  replicas: 1
  port: 5000
  resources:
    requests:
      cpu: "1"
      memory: "4Gi"
    limits:
      cpu: "4"
      memory: "8Gi"
  tls:
    enabled: true
  ingress:
    enabled: true
    host: inference.customer-domain.com
    path: /models/phi-3-mini(/|$)(.*)
    rewritePath: /$2
    tls:
      enabled: true
      secretName: my-custom-tls
```

## Authentication

Foundry Local on Azure Local secures all inference endpoints with API key authentication enabled by default. When you deploy a model, the inference operator automatically generates a primary and secondary API key pair, stores them in a Kubernetes secret named `<deployment-name>-api-keys`, and requires a valid key on every request. A request without a valid key is rejected with `401 Unauthorized`.

The dual-key design means both keys are valid simultaneously, so you can rotate one key while clients continue using the other - with no interruption in service.

### Retrieve API keys

To get the primary API key for a deployment named `my-deployment`:

```bash
kubectl get secret my-deployment-api-keys -n my-deployment-ns \
  -o jsonpath='{.data.primary-key}' | base64 --decode
```

To get the secondary key, use `secondary-key` in the JSON path. Keep these values safe - they're the credentials required to call the model's REST API.

The ModelDeployment owns the secret, so it's deleted automatically if you remove the deployment.

### Use API keys in requests

Include the API key in your HTTP request by using any of these header formats:

- **Authorization header** (Bearer token):
  ```
  Authorization: Bearer $API_KEY
  ```
- **X-API-Key header**:
  ```
  X-API-KEY: $API_KEY
  ```
- **api-key header** (OpenAI-compatible):
  ```
  api-key: $API_KEY
  ```

All inference endpoints - for both generative and predictive models - require an API key. This requirement applies to `/v1/chat/completions` (generative) and `/v1/predict` (predictive) endpoints. If the key matches either the primary or secondary key for the target deployment, the request succeeds. Otherwise, the NGINX auth proxy rejects the request before it reaches the model.

### Rotate API keys

To rotate a key without downtime, use a two-step swap:

1. Choose which key to rotate (primary or secondary).
1. Update the Kubernetes secret with a new value for that key:

   ```bash
   kubectl patch secret my-deployment-api-keys -n my-deployment-ns --type='json' \
     -p='[
       {"op": "replace", "path": "/data/primary-key", "value": "$NEW_PRIMARY_KEY"},
       {"op": "replace", "path": "/data/primary-rotated-at", "value": "$CURRENT_TIMESTAMP"}
     ]'
   ```

1. The deployment now has one old key (secondary) and one new key (primary). Clients can continue using the unchanged secondary key during this transition.
1. Update your clients to use the new primary key.
1. Rotate the secondary key by using the same command.

After this two-step swap, you have entirely new primary and secondary keys with no interruption in service.

> [!NOTE]
> In the preview, manage key rotation manually as described in this article. When you update the API keys secret, the NGINX sidecar picks up the change within a few seconds and you can use the new key immediately. A dedicated rotation command might be available in a future release.

## Related content

- [Run inference on Foundry Local on Azure Local](how-to-run-inference.md)
- [Inference API endpoints and payload reference](reference-inference-api-endpoints-payload.md)
- [ModelDeployment and operator configuration reference](reference-model-deployment-operator.md)
- [Request deployment access](what-is-foundry-local-on-azure-local.md#request-deployment-access)
