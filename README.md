# Ollama

Environment values for running [Ollama](https://ollama.com) on Kubernetes.

This repo ships **values only**. There is no `Chart.yaml` and no `templates/`: the chart itself is
the community [otwld/ollama-helm](https://github.com/otwld/ollama-helm) chart, pulled from
`https://helm.otwld.com/`, and these files configure it.

## Values files

| File | Purpose |
|---|---|
| `base.values.yaml` | Defaults shared across all environments |
| `lcl.values.yaml` | Local Kubernetes cluster overrides |
| `snbx.values.yaml` | Sandbox GKE overrides (adds `persistence.storageClass: standard-rwo`) |

Two settings matter more than the rest:

1. `OLLAMA_ORIGINS` must list the callers when Ollama sits behind an API-style server, or requests
   are rejected as cross-origin.
2. `ollama.models.pull` and `ollama.models.run` must name the models, or the pod starts with
   nothing loaded.

Every other `OLLAMA_*` variable is documented in
[envconfig/config.go](https://github.com/ollama/ollama/blob/main/envconfig/config.go).

## Usage

```shell
helm repo add otwld https://helm.otwld.com/
helm upgrade -install ollama-chart otwld/ollama -f base.values.yaml -f lcl.values.yaml -n default
```

Swap `lcl.values.yaml` for `snbx.values.yaml` on the sandbox cluster.

## FluxCD GitOps

In the cluster this is reconciled by `fluxcd-ollama-chart.yaml`: a `HelmRepository` pointing at
`helm.otwld.com`, a `HelmRelease` with `releaseName: ollama-chart` tracking chart version `1.x.x`,
and values supplied through the generated `ollama-chart-values` ConfigMap rather than these files
directly. An `ImagePolicy` tracks `docker.io/ollama/ollama`.

## References

- https://github.com/otwld/ollama-helm **Community chart used now**
- https://github.com/ollama/ollama
- https://hub.docker.com/r/ollama/ollama
- https://github.com/otwld/ollama-helm/blob/main/values.yaml
- https://ollama.com/blog/ollama-is-now-available-as-an-official-docker-image
- https://github.com/ollama/ollama/blob/main/envconfig/config.go **Very important for the Ollama environment variables to configure!**

## License

[Elastic License 2.0](./LICENSE) — source-available; not OSI open source.
