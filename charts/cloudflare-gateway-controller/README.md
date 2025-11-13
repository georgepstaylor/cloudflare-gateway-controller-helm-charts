# Cloudflare Gateway Controller Helm Chart

A Kubernetes Gateway API controller that uses Cloudflare Tunnels to expose services to the internet.

## Prerequisites

- Kubernetes 1.19+
- Helm 3.0+
- Gateway API CRDs v1.2.1+ installed
- Cloudflare account with:
  - API token with Cloudflare Tunnel and DNS edit permissions
  - Account ID

## Installation

### Install Gateway API CRDs (if not already installed)

```bash
kubectl apply -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.2.1/standard-install.yaml
```

### Install the Helm chart

```bash
helm install cloudflare-gateway-controller ./charts/cloudflare-gateway-controller \
  --namespace cloudflare-gateway-system \
  --create-namespace \
  --set cloudflare.apiToken=YOUR_API_TOKEN \
  --set cloudflare.accountId=YOUR_ACCOUNT_ID \
  --set cluster.domain=cluster.local
```

## Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `replicaCount` | Number of controller replicas | `1` |
| `image.repository` | Controller image repository | `ghcr.io/georgepstaylor/cloudflare-gateway-controller` |
| `image.tag` | Controller image tag | `latest` |
| `image.pullPolicy` | Image pull policy | `IfNotPresent` |
| `cloudflare.apiToken` | Cloudflare API token | `""` |
| `cloudflare.accountId` | Cloudflare account ID | `""` |
| `cloudflare.createSecret` | Create secret from values | `true` |
| `cloudflare.existingSecret` | Use existing secret | `""` |
| `cluster.domain` | Kubernetes cluster DNS domain | `cluster.local` |
| `resources.limits.cpu` | CPU limit | `500m` |
| `resources.limits.memory` | Memory limit | `128Mi` |
| `resources.requests.cpu` | CPU request | `10m` |
| `resources.requests.memory` | Memory request | `64Mi` |

## Usage

Create a GatewayClass:

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: GatewayClass
metadata:
  name: cloudflare
spec:
  controllerName: george.dev/cloudflare-gateway-controller
  parametersRef:
    group: ""
    kind: Secret
    name: cloudflare-credentials
    namespace: cloudflare-gateway-system
```

Create a Gateway:

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: my-gateway
  namespace: default
spec:
  gatewayClassName: cloudflare
  listeners:
    - name: http
      protocol: HTTP
      port: 80
```

Create an HTTPRoute:

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: my-app
  namespace: default
  annotations:
    cloudflare-gateway-controller.github.com/no-tls-verify: "true"
spec:
  parentRefs:
    - name: my-gateway
  hostnames:
    - app.example.com
  rules:
    - backendRefs:
        - name: my-service
          port: 80
```

## Annotations

### HTTPRoute Annotations

- `cloudflare-gateway-controller.github.com/no-tls-verify`: Set to `"true"` to disable TLS verification for HTTPS backends
- `cloudflare-gateway-controller.github.com/backend-protocol`: Set to `"https"` to use HTTPS for backend connections

## Uninstallation

```bash
helm uninstall cloudflare-gateway-controller -n cloudflare-gateway-system
```
