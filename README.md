# ITL.K8s.MetricsServer

[metrics-server](https://github.com/kubernetes-sigs/metrics-server) for the ITL Talos cluster ([`ITlusions/ITL.K8s`](https://github.com/ITlusions/ITL.K8s)) - a thin wrapper chart around the upstream chart, same pattern as [`ITL.Longhorn`](https://github.com/ITlusions/ITL.Longhorn) and [`ITL.Traefik`](https://github.com/ITlusions/ITL.Traefik). Needed for `kubectl top` and any `HorizontalPodAutoscaler` with CPU/memory targets.

## Deploy

```bash
cd itl.metrics-server
helm dependency update
helm upgrade --install itl-metrics-server . \
  -n kube-system \
  --wait --timeout 3m
```

## Why `--kubelet-insecure-tls`

Talos's kubelet presents a self-signed serving certificate - there's no cluster CA metrics-server trusts by default. Without this flag, metrics-server can reach kubelet but TLS verification fails, and every `kubectl top` or HPA metrics query comes back empty. This is metrics-server's own [documented Talos requirement](https://docs.siderolabs.com/kubernetes-guides/monitoring/metrics-server), not a security shortcut specific to this cluster.

## Verify

```bash
kubectl get pods -n kube-system -l app.kubernetes.io/name=metrics-server
kubectl top nodes
kubectl top pods -A
```
