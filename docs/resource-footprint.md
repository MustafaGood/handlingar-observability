# Resource footprint

- **Status:** not measured
- **Date:** 2026-10-05
- **Budget:** 1.5 vCPU and 3 GB memory requests for the whole stack

## Result

No container in this stack has been run yet, so there is no measured CPU,
memory, or disk figure. The workstation container engine returned an error
on 2026-10-05 and could not start a cluster.

Do not treat the ADR request table as a measurement. Fill the table below
from a running sandbox before the ADR is accepted.

## How to measure

After `sandbox/bootstrap.sh` finishes and the pods are Ready:

```bash
kubectl -n observability top pods
kubectl top nodes
kubectl -n observability get pvc
```

Record one row per pod: CPU and memory from `kubectl top`, plus the request
and limit from the pod spec. Sum the requests. The sum must stay under
1.5 vCPU and 3 GB. DaemonSets are counted once per worker.

Disk is the Prometheus PVC and the Loki PVC (`kubectl get pvc -n observability`).

## Request budget

Copied from the ADR. These are the requests the values files ask for, on
3 workers.

| Component | CPU request | Memory request |
| --- | --- | --- |
| Prometheus | 200m | 1 GiB |
| Prometheus Operator | 50m | 128 MiB |
| Alertmanager | 50m | 128 MiB |
| Grafana | 100m | 256 MiB |
| kube-state-metrics | 50m | 128 MiB |
| node-exporter x3 | 150m | 192 MiB |
| Alloy x3 | 150m | 384 MiB |
| Loki | 100m | 384 MiB |
| blackbox exporter | 20m | 64 MiB |
| Postgres exporter | 20m | 64 MiB |
| Redis exporter | 20m | 64 MiB |
| Memcached exporter | 20m | 64 MiB |
| **Sum** | **about 980m** | **about 2.9 GiB** |

## Measured usage

| Pod | CPU | Memory | Restarts | Notes |
| --- | --- | --- | --- | --- |
| — | — | — | — | not measured |
