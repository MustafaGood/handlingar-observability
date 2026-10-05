# Reference sandbox

The sandbox copies the target cluster closely enough to install this package:
K3s v1.32, Cilium, Traefik, a ClusterIssuer named `letsencrypt-prod`, and
workloads named like Alaveteli.

## What is not the real application

A full Alaveteli process is large, and on 2026-10-05 the workstation container
engine did not start, so this sandbox was not executed. The web and Sidekiq
deployments are a small HTTP process with the same names, labels, and port
3000. Postgres, Redis, Memcached, and Mailpit are real images. The metrics
and log path does not depend on Rails for the first version.

## Run

Export these variables in the shell. Do not write the values into a file in
this repository.

- `SANDBOX_DB_PASSWORD`
- `SANDBOX_REDIS_PASSWORD`
- `SANDBOX_GRAFANA_USER`
- `SANDBOX_GRAFANA_PASSWORD`
- `SANDBOX_ALERT_FROM`
- `SANDBOX_ALERT_TO`

Commands required: `docker`, `k3d`, `kubectl`, `helm`, `cilium`.

```bash
./sandbox/bootstrap.sh
```

`bootstrap.sh` leaves PVCs behind on delete. Remove the cluster with
`k3d cluster delete handlingar-obs` when the test is finished.

Blackbox probes use `sandbox/values-blackbox.yaml`, which points at the
in-cluster Services instead of the public hostnames.
