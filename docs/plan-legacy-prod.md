# Plan: scrape the legacy production server

This is a plan only. Do not install anything on the production server from
this repository. Production is still the single server outside the cluster.

## Goal

Scrape node metrics and Postfix queue metrics from that server into the
cluster Prometheus, after the cluster stack is accepted.

## Path

1. Run node-exporter on the server, bound to the private address, not the
   public address.
2. Run a Postfix exporter on the server the same way. The Mail dashboard
   already queries `postfix_queue_messages`. It stays empty until this exists.
3. Allow ingress on those ports only from the cluster egress address.
4. Put the credentials in a secret created by External Secrets. Do not write
   them in git.
5. Add a Prometheus static scrape, or a probe, that uses TLS. The target
   address is a parameter, not a value committed here.
6. Add a NetworkPolicy egress from Prometheus to that address and port, and
   the matching Cilium rule if the host is named rather than addressed.

## If this is not done

The cluster dashboards do not show the legacy server. The Postfix panel stays
empty. The cluster stack does not depend on this plan.
