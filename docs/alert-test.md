# Alert test

No alert has been fired yet. The workstation container engine did not start,
so there is no record of false alarms from a live run.

## How to fire SiteDown on purpose

```bash
make obs-alert-test
```

That scales `alaveteli-web` in `tst` to 0 replicas. The blackbox probe for
`env="tst"` should be 0 for 3 minutes, then SiteDown fires and Mailpit
receives the mail.

```bash
make obs-alert-restore
```

When the pod is Ready again, `probe_success` returns to 1. Alertmanager is
configured with `send_resolved: true`, so the mail is cleared.

## Adjustments made before the first live run

- SiteDown matches only the `handlingar`, `tst`, and `qat` probes.
- HighErrorRate stays quiet when a service has no requests, so an empty
  series is not treated as 100 percent errors.
- SidekiqNotReady uses desired replicas that are not available. Queue depth
  is not a metric until the application exposes it.
- PostgresConnectionsHigh groups by the database server, not the exporter pod.
- PodCrashLoop uses the 15 minute restart increase on its own, without an
  extra wait on top.
