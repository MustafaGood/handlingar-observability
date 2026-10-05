# Troubleshooting with Grafana and Loki

Paste extracts into a session only after the repository masking tool has been
used. The supervisor links that tool. Do not paste secrets.

## R1 — Site down

Preconditions: the SiteDown mail has arrived, and Grafana is open.

1. Open the Overview dashboard and set `env` to the namespace named in the mail.
2. Read the Site up panel. `0` means the blackbox probe failed.
3. In Explore, select the Loki datasource and run:

```
{namespace=~"$env", app="alaveteli"}
```

Replace `$env` with the namespace from the mail.

4. Check the pod:

```bash
kubectl -n tst get pods -l app=alaveteli
kubectl -n tst describe pod -l app=alaveteli
```

Use the namespace from the mail instead of `tst` when it is different.

How to verify it worked: Site up returns to `1`, and SiteDown is no longer firing.

How to undo: if you scaled the web deployment down for a test, restore it:

```bash
make obs-alert-restore
```

## R2 — Sidekiq queue growing

Preconditions: SidekiqNotReady fired, or failed jobs are visible in the logs.

1. Open the Alaveteli dashboard and set `env`.
2. Read Sidekiq ready. `0` means the pod is not Ready.
3. In Explore, run:

```
{namespace=~"$env", app="alaveteli-sidekiq"} |~ "(?i)(error|exception)"
```

4. Queue depth is not a metric until the yabeda proposal is applied. Use the log lines and the pod status.

How to verify it worked: the Sidekiq pod is Ready and new error lines stop.

How to undo: revert the deployment change that was made while testing. Do not delete the queue.

## R3 — Certificate expiring

Preconditions: CertificateExpiry fired.

1. Open Overview and read Certificate days left.
2. In Explore, run:

```
{namespace="cert-manager"} |~ "(?i)error"
```

3. List the certificates:

```bash
kubectl get certificate -A
```

How to verify it worked: days left is above 7, and CertificateExpiry has cleared.

How to undo: cert-manager renews the certificate. Do not delete the secret unless the issuer is known to recreate it.

## R4 — Disk full

Preconditions: DiskFilling fired.

1. Open the Cluster dashboard.
2. Read Node disk and PVC fill. Note the instance or the volume name.
3. List volumes:

```bash
kubectl get pvc -A
```

How to verify it worked: used space is under 85 percent and DiskFilling has cleared.

How to undo: if a test file was written to fill the disk, delete that file. Do not delete Prometheus or Loki volumes to silence the alert.

## R5 — Exit test

Preconditions: Mailpit is the SMTP target, and the stack is up.

1. Run `make obs-alert-test`. That scales `alaveteli-web` in `tst` to 0 replicas.
2. Wait 3 minutes. The SiteDown mail should arrive in Mailpit.
3. Run `make obs-alert-restore`.
4. Wait until the pod is Ready. SiteDown should clear and a resolved mail should arrive.

How to verify it worked: one firing mail and one resolved mail are in Mailpit.

How to undo: `make obs-alert-restore`.
