# Helm Chart for singletrail-map.ch

This repository contains a Helm umbrella chart intended to deploy the Singletrail Map stack. It will eventually
aggregate multiple services. Currently, it includes `tileserver-gl` as a dependency and keeps its defaults unless
you override them via this umbrella chart's `values.yaml`.

## Contents
- Chart location: `singletrailmap-chart/`
- Current dependency: `tileserver-gl` (from `https://joe-akeem.github.io/tileserver-gl-helm-chart`)

## Prerequisites
- Kubernetes cluster (1.24+ recommended)
- Helm 3.8+

## Getting started

1) Update dependencies

```
cd singletrailmap-chart
helm dependency update
```

This pulls subcharts (e.g., `tileserver-gl`) based on `Chart.yaml`.

2) Install the chart

```
# Replace <release-name> and <namespace> as needed
helm install <release-name> ./singletrailmap-chart -n <namespace> --create-namespace
```

3) Upgrade (when changing values)

```
helm upgrade <release-name> ./singletrailmap-chart -n <namespace> -f my-values.yaml
```

4) Optional: Port forwarding with kubectl

```
kubectl -n <namespace> port-forward svc/singletrail-map-tileserver-gl-helm-chart 8080:8080
```

5) Uninstall

```
helm uninstall <release-name> -n <namespace>
```

## Configuring tileserver-gl via this umbrella chart

You can override any tileserver-gl chart value by nesting it under the `tileserver-gl` key in your values.yaml file.
The umbrella chart ships with an empty `tileserver-gl: {}` block to keep upstream defaults.

Examples (pick what you need):

```
# my-values.yaml

tileserver-gl:
  # Example: expose via LoadBalancer
  service:
    type: LoadBalancer

  # Example: enable persistence for MBTiles or cache
  persistence:
    enabled: true
    size: 10Gi

  # Example: set resources
  resources:
    requests:
      cpu: "200m"
      memory: "512Mi"
    limits:
      cpu: "500m"
      memory: "1Gi"

  # Example: custom environment / config (depends on tileserver-gl chart)
  # env:
  #   - name: TILESERVER_OPTIONS
  #     value: "--publicUrl=https://tiles.example.com"
```

Apply with:

```
helm upgrade --install <release-name> ./singletrailmap-chart -n <namespace> -f my-values.yaml
```

## Global values

The umbrella `values.yaml` includes a `global` section for future cross-service settings (consumed by subcharts that support `global.*`). For now it contains placeholders:

```
global:
  imagePullSecrets: []
  commonLabels: {}
```

## Development notes
- When adding new services to this umbrella chart, declare them under `dependencies:` in `singletrailmap-chart/Chart.yaml`
  and document their values under a section like `<subchart-name>:` in `values.yaml`.
- After editing `Chart.yaml` or bumping dependency versions, run `helm dependency update` again.
- If you publish this umbrella chart to a chart repository, consumers will not need to run `helm dependency update` locally;
  instead, keep `charts/` packaged or rely on `helm pull` from the repo.

## Troubleshooting
- If dependency charts are not found, ensure `helm dependency update` succeeded and the repository URL in `Chart.yaml` is reachable.
- Confirm the Kubernetes service type and ingress settings according to your cluster environment (e.g., LoadBalancer
  availability on your cloud or MetalLB on kind/bare metal).

