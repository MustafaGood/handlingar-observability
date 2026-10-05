# handlingar-observability

Observability package for the handlingar.se K3s cluster: metrics, logs, and
email alerts. The supervisor copies this repository into
`infra/k8s/observability/` after review.

## Status

The stack is **not installed yet**. T1 is the decision record, and it gates
the Helm values, dashboards, and alerts.

- Decision: [docs/adr-observability-stack.md](docs/adr-observability-stack.md)
- Status: **proposed** (2026-10-05)
- Measured CPU, memory, and disk: not available until the sandbox run in T2

## Charts

Pinned in the ADR. Grafana is not its own application; chart
`kube-prometheus-stack` 91.9.0 includes it.

| Chart | Version | Repository |
| --- | --- | --- |
| `kube-prometheus-stack` | 91.9.0 | `https://prometheus-community.github.io/helm-charts` |
| `loki` | 7.3.0 | `https://grafana.github.io/helm-charts` |
| `alloy` | 1.13.0 | `https://grafana.github.io/helm-charts` |
| `prometheus-blackbox-exporter` | 11.19.1 | `https://prometheus-community.github.io/helm-charts` |
| `prometheus-postgres-exporter` | 8.2.0 | `https://prometheus-community.github.io/helm-charts` |
| `prometheus-redis-exporter` | 6.33.0 | `https://prometheus-community.github.io/helm-charts` |
| `prometheus-memcached-exporter` | 0.6.0 | `https://prometheus-community.github.io/helm-charts` |

Integration steps, DNS records, and `make obs-*` targets are written when T2
lands. They are not invented here.
