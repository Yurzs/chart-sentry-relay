# Sentry Relay Helm Chart

A Helm chart for deploying [Sentry Relay](https://github.com/getsentry/relay) to Kubernetes.

Sentry Relay is a service that pushes some functionality from the Sentry SDKs as well as the Sentry server into a proxy process, improving performance, reducing infrastructure load, and allowing for additional data scrubbing.

## Prerequisites

- Kubernetes 1.19+
- Helm 3.0+

## Installation

### Install from OCI registry (ghcr.io)

```bash
helm install my-relay oci://ghcr.io/yurzs/chart-sentry-relay/sentry-relay --version 0.1.0
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
| `relay.overrideProjectIds` | Override project IDs in client DSNs | `false` |
| `extraConfig` | Additional relay configuration | `{}` |

### Logging Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `logging.level` | Log level: off, error, warn, info, debug, trace | `info` |
| `logging.format` | Log format: auto, pretty, simplified, json | `auto` |
| `logging.enableBacktraces` | Write backtraces for internal errors | `true` |

### HTTP Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `http.timeout` | Timeout for upstream requests in seconds | `5` |
| `http.connectionTimeout` | Connection timeout in seconds | `3` |
| `http.maxRetryInterval` | Max retry interval in seconds | `60` |
| `http.hostHeader` | Custom HTTP Host header | `null` |

### Caching Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `cache.projectExpiry` | Project config cache timeout (seconds) | `300` |
| `cache.projectGracePeriod` | Grace period after expiry (seconds) | `0` |
| `cache.relayExpiry` | Downstream relay info cache (seconds) | `3600` |
| `cache.envelopeExpiry` | Max envelope buffer time (seconds) | `600` |
| `cache.missExpiry` | Non-existing entries timeout (seconds) | `60` |
| `cache.batchInterval` | Batch query interval (ms) | `100` |
| `cache.batchSize` | Max project configs per batch | `500` |
| `cache.fileInterval` | Local cache file watch interval (seconds) | `10` |
| `cache.envelopeBufferSize` | Max buffered payloads | `1000` |
| `cache.evictionInterval` | Config eviction interval (seconds) | `60` |

### Spooling Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `spool.envelopes.path` | Spool file path (null to disable) | `null` |
| `spool.envelopes.maxMemorySize` | Max in-memory buffer | `500MB` |
| `spool.envelopes.maxDiskSize` | Max on-disk spool size | `500MB` |
| `spool.envelopes.maxConnections` | Max spool connections | `20` |
| `spool.envelopes.minConnections` | Min spool connections | `10` |

### Size Limits Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `limits.maxConcurrentRequests` | Max concurrent upstream connections | `100` |
| `limits.maxConcurrentQueries` | Max concurrent queries | `5` |
| `limits.maxEventSize` | Max event payload size | `1MiB` |
| `limits.maxAttachmentSize` | Max single attachment size | `100MiB` |
| `limits.maxAttachmentsSize` | Max combined attachments size | `100MiB` |
| `limits.maxEnvelopeSize` | Max envelope size | `100MiB` |
| `limits.maxSessionCount` | Max sessions per envelope | `100` |
| `limits.maxApiPayloadSize` | Max API payload size | `20MiB` |
| `limits.maxApiFileUploadSize` | Max file upload size | `40MiB` |
| `limits.maxApiChunkUploadSize` | Max chunk upload size | `100MiB` |
| `limits.maxThreadCount` | Max threads per worker | `null` (auto) |
| `limits.queryTimeout` | Query timeout in seconds | `30` |
| `limits.shutdownTimeout` | Shutdown timeout in seconds | `10` |

### Metrics Configuration (StatsD)

| Parameter | Description | Default |
|-----------|-------------|---------|
| `metrics.statsd` | StatsD host:port (null to disable) | `null` |
| `metrics.prefix` | Metrics prefix | `sentry.relay` |
| `metrics.defaultTags` | Default tags for metrics | `{}` |
| `metrics.hostnameTag` | Hostname tag name | `null` |
| `metrics.buffering` | Buffer metrics before sending | `true` |
| `metrics.sampleRate` | Sample rate (0.0-1.0) | `1.0` |

### Internal Error Reporting

| Parameter | Description | Default |
|-----------|-------------|---------|
| `sentry.enabled` | Enable internal error reporting | `false` |
| `sentry.dsn` | Sentry DSN for internal errors | `""` |

### GeoIP Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `geoip.path` | Path to MaxMind GeoIP database | `null` |

### Processing Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `processing.enabled` | Enable event processing | `false` |
| `processing.kafka` | Kafka configuration | `{}` |
| `processing.redis` | Redis URL | `""` |

### Outcomes Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `outcomes.emitOutcomes` | Emit outcomes to upstream | `true` |
| `outcomes.batchSize` | Batch size for outcomes | `1000` |
| `outcomes.batchInterval` | Batch interval (ms) | `500` |

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

### With StatsD Metrics

```bash
helm install my-relay ./sentry-relay \
  --set metrics.statsd="statsd.monitoring:8125" \
  --set metrics.prefix="myapp.relay"
```

### With Internal Error Reporting

```bash
helm install my-relay ./sentry-relay \
  --set sentry.enabled=true \
  --set sentry.dsn="https://key@sentry.io/123"
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

http:
  timeout: 10
  connectionTimeout: 5

cache:
  projectExpiry: 600
  envelopeBufferSize: 2000

limits:
  maxConcurrentRequests: 200
  shutdownTimeout: 30

metrics:
  statsd: "statsd.monitoring:8125"
  prefix: "production.relay"
  hostnameTag: "host"

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

