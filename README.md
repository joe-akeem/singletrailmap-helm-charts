# Helm Chart for singletrail-map.ch

## Installing

Update dependencies:

```shell
cd singletrailmap-chart
helm dependency update
```

Install singletrail-map:
```shell
 helm install singletrailmap ./singletrailmap-chart
```

## Local Testing

For local testing, start a [kind](https://kind.sigs.k8s.io/docs/user/quick-start/) cluster and configure `kubectl` accordingly:
```shell
kind create cluster --config kind-config.yaml
kubectl config use-context kind-kind
```

Create an Ingress
```shell
kubectl apply -f https://kind.sigs.k8s.io/examples/ingress/deploy-ingress-nginx.yaml
```

Wait for Ingress to be ready
```shell
kubectl wait --namespace ingress-nginx \
  --for=condition=ready pod \
  --selector=app.kubernetes.io/component=controller \
  --timeout=90s
```

When done, delete the kind cluster.
```shell
kind delete cluster
```
