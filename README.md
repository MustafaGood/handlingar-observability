# handlingar-observability

Observability package for the handlingar.se K3s cluster: metrics, logs, and
email alerts. The supervisor copies this repository into
`infra/k8s/observability/` after review.

## Status

| Piece | State |
| --- | --- |
| ADR | proposed, not accepted |
| Measured CPU and memory | not measured |
| Helm values and manifests | written, rendered with Helm, not installed |
| Sandbox | scripted, not executed |

Decision: [docs/adr-observability-stack.md](docs/adr-observability-stack.md)

## Charts

Install each chart in namespace `observability` with the release name in the
first column. Grafana is inside `kube-prometheus-stack`, not its own release.

| Release | Chart | Version | Values | Repository |
| --- | --- | --- | --- | --- |
| `kube-prometheus-stack` | `kube-prometheus-stack` | 91.9.0 | `values-kube-prometheus-stack.yaml` | `https://prometheus-community.github.io/helm-charts` |
| `loki` | `loki` | 7.3.0 | `values-loki.yaml` | `https://grafana.github.io/helm-charts` |
| `alloy` | `alloy` | 1.13.0 | `values-alloy.yaml` | `https://grafana.github.io/helm-charts` |
| `blackbox-exporter` | `prometheus-blackbox-exporter` | 11.19.1 | `values-blackbox-exporter.yaml` | `https://prometheus-community.github.io/helm-charts` |
| `postgres-exporter` | `prometheus-postgres-exporter` | 8.2.0 | `values-postgres-exporter.yaml` | `https://prometheus-community.github.io/helm-charts` |
| `redis-exporter` | `prometheus-redis-exporter` | 6.33.0 | `values-redis-exporter.yaml` | `https://prometheus-community.github.io/helm-charts` |
| `memcached-exporter` | `prometheus-memcached-exporter` | 0.6.0 | `values-memcached-exporter.yaml` | `https://prometheus-community.github.io/helm-charts` |

## DNS to approve before apply

| Name | Type | Value |
| --- | --- | --- |
| `grafana.nonprod.handlingar.se` | CNAME or A, created by external-dns from the IngressRoute | the Traefik load balancer |

No other name is created by this package.

## Secrets

Created by the supervisor from the ExternalSecret templates. This repo has no
secret values.

| Secret | Template |
| --- | --- |
| `grafana-admin` | `manifests/externalsecret-grafana-admin.yaml` |
| Alertmanager config | `manifests/externalsecret-alert-email.yaml` |
| `postgres-exporter-dsn` key `dsn` | created by the supervisor, no template value |
| `redis-exporter` key `password` | created by the supervisor, no template value |

The store name in the templates is `cluster-secret-store`.

## Local manifests

```bash
kubectl apply -k .
```

`proposals/` is not applied by that command. See each file for what breaks if
it is skipped.

## Make targets

`make obs-up`, `make obs-status`, `make obs-alert-test`, `make obs-down`.

Helm releases are not installed by `make obs-up`. Install them with the table
above, then apply the local manifests.

## Uninstall

Helm uninstall removes the releases. `make obs-down` removes the local
manifests. PersistentVolumeClaims for Prometheus and Loki are kept.
