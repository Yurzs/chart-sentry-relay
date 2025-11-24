# Sentry Relay Helm Chart

A Helm chart for deploying [Sentry Relay](https://github.com/getsentry/relay) to Kubernetes.

Sentry Relay is a service that pushes some functionality from the Sentry SDKs as well as the Sentry server into a proxy process, improving performance, reducing infrastructure load, and allowing for additional data scrubbing.

## Prerequisites

- Kubernetes 1.19+
- Helm 3.0+

## Installation

### Install from OCI registry (ghcr.io)

```bash
helm install my-relay oci://ghcr.io/yurzs/charts/sentry-relay --version 0.1.0
```

### Install directly from source

```bash
git clone https://github.com/Yurzs/chart-sentry-relay.git
cd chart-sentry-relay
helm install my-relay ./sentry-relay
```

## Configuration

The following table lists the configurable parameters of the Sentry Relay chart and their default values.

### General Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `replicaCount` | Number of relay replicas | `1` |
| `image.repository` | Image repository | `getsentry/relay` |
| `image.pullPolicy` | Image pull policy | `IfNotPresent` |
| `image.tag` | Image tag (defaults to chart appVersion) | `""` |
| `imagePullSecrets` | Image pull secrets | `[]` |
| `nameOverride` | Override chart name | `""` |
| `fullnameOverride` | Override fully qualified app name | `""` |

### Relay Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `relay.mode` | Relay mode: managed, static, proxy, or capture | `managed` |
| `relay.upstream` | Upstream Sentry server URL | `https://sentry.io` |
| `relay.host` | Host to bind to | `0.0.0.0` |
| `relay.port` | Port to listen on | `3000` |
| `relay.logging.level` | Log level: off, error, warn, info, debug, trace | `info` |
| `relay.logging.format` | Log format: human or json | `human` |
| `relay.processing.enabled` | Enable event processing | `false` |
| `relay.outcomes.emitOutcomes` | Emit outcomes to upstream | `true` |
| `relay.outcomes.batchSize` | Batch size for outcomes | `1000` |
| `relay.outcomes.batchInterval` | Batch interval in milliseconds | `500` |
| `extraConfig` | Additional relay configuration | `{}` |

### Credentials Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `credentials.existingSecret` | Use existing secret for credentials | `""` |
| `credentials.secretKey` | Secret key containing credentials JSON | `credentials.json` |

### Service Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `service.type` | Kubernetes service type | `ClusterIP` |
| `service.port` | Service port | `3000` |

### Ingress Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `ingress.enabled` | Enable ingress | `false` |
| `ingress.className` | Ingress class name | `""` |
| `ingress.annotations` | Ingress annotations | `{}` |
| `ingress.hosts` | Ingress hosts configuration | See values.yaml |
| `ingress.tls` | Ingress TLS configuration | `[]` |

### Resource Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `resources` | CPU/Memory resource requests/limits | `{}` |
| `autoscaling.enabled` | Enable HPA | `false` |
| `autoscaling.minReplicas` | Minimum replicas | `1` |
| `autoscaling.maxReplicas` | Maximum replicas | `10` |

## Relay Modes

Sentry Relay can operate in different modes:

- **managed** (default): Relay registers with Sentry and receives configuration automatically. Recommended for most use cases with Sentry.io or self-hosted Sentry.

- **static**: Relay uses predefined configuration. Project configurations must be provided in the config file.

- **proxy**: Relay forwards all events without processing. Useful for simple forwarding scenarios.

- **capture**: Stores events locally for debugging purposes.

## Examples

### Basic Installation with Sentry.io

```bash
helm install my-relay ./sentry-relay \
  --set relay.upstream="https://sentry.io"
```

### Self-hosted Sentry

```bash
helm install my-relay ./sentry-relay \
  --set relay.upstream="https://sentry.mycompany.com"
```

### Proxy Mode

```bash
helm install my-relay ./sentry-relay \
  --set relay.mode=proxy \
  --set relay.upstream="https://sentry.io"
```

### With Existing Credentials

```bash
# First create the secret with your credentials
kubectl create secret generic relay-credentials \
  --from-file=credentials.json=/path/to/credentials.json

# Then install with the secret
helm install my-relay ./sentry-relay \
  --set credentials.existingSecret=relay-credentials
```

### With Ingress

```bash
helm install my-relay ./sentry-relay \
  --set ingress.enabled=true \
  --set ingress.hosts[0].host=relay.example.com \
  --set ingress.hosts[0].paths[0].path=/ \
  --set ingress.hosts[0].paths[0].pathType=Prefix
```

### Production Configuration

```yaml
# values-production.yaml
replicaCount: 3

relay:
  mode: managed
  upstream: "https://sentry.mycompany.com"
  logging:
    level: warn
    format: json

resources:
  limits:
    cpu: 500m
    memory: 512Mi
  requests:
    cpu: 100m
    memory: 256Mi

autoscaling:
  enabled: true
  minReplicas: 3
  maxReplicas: 10
  targetCPUUtilizationPercentage: 70

affinity:
  podAntiAffinity:
    preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 100
        podAffinityTerm:
          labelSelector:
            matchExpressions:
              - key: app.kubernetes.io/name
                operator: In
                values:
                  - sentry-relay
          topologyKey: kubernetes.io/hostname
```

```bash
helm install my-relay ./sentry-relay -f values-production.yaml
```

## License

MIT License - see [LICENSE](LICENSE) for details.

