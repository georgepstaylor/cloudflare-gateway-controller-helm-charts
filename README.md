# Cloudflare Gateway Controller Helm Charts

This repository contains Helm charts for the [Cloudflare Gateway Controller](https://github.com/georgepstaylor/cloudflare-gateway-controller).

## Overview

The Cloudflare Gateway Controller is a Kubernetes Gateway API controller that uses Cloudflare Tunnels to expose services to the internet.

## Installation

### Add Helm Repository

```bash
helm repo add cloudflare-gateway-controller https://georgepstaylor.github.io/cloudflare-gateway-controller-helm-charts
helm repo update
```

### Install Chart

```bash
helm install cloudflare-gateway-controller \
  cloudflare-gateway-controller/cloudflare-gateway-controller \
  --namespace cloudflare-gateway-controller \
  --create-namespace \
  --set cloudflare.accountId=YOUR_ACCOUNT_ID \
  --set cloudflare.apiToken=YOUR_API_TOKEN
```

## Configuration

See the chart [README](charts/cloudflare-gateway-controller/README.md) for detailed configuration options.

## Development

### Prerequisites

- [Helm](https://helm.sh/docs/intro/install/) 3.x
- [kubectl](https://kubernetes.io/docs/tasks/tools/)
- Kubernetes cluster (for testing)

### Testing Locally

```bash
# Lint the chart
helm lint charts/cloudflare-gateway-controller

# Template the chart
helm template cloudflare-gateway-controller charts/cloudflare-gateway-controller

# Install locally
helm install cloudflare-gateway-controller charts/cloudflare-gateway-controller \
  --namespace cloudflare-gateway-controller \
  --create-namespace \
  --set cloudflare.accountId=YOUR_ACCOUNT_ID \
  --set cloudflare.apiToken=YOUR_API_TOKEN
```

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is licensed under the Apache License 2.0 - see the [LICENSE](LICENSE) file for details.
