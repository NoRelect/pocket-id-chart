# pocket-id Helm Chart

Helm chart for [Pocket ID](https://pocket-id.org).

## Installation

Create the namespace and generate a secret:

```sh
kubectl create namespace pocket-id
kubectl create secret generic pocket-id \
  --namespace pocket-id \
  --from-literal=encryption-key="$(openssl rand -hex 32)"
```

Create the `my-values.yaml` file:

```yaml
config:
  appUrl: "https://auth.example.com"
  trustProxy: true
  allowUserSignups: "disabled"   # disabled | withToken | open
  sessionDuration: 60            # minutes

secret:
  existingSecret: "pocket-id"

ingress:
  enabled: true
  className: nginx
  annotations:
    cert-manager.io/cluster-issuer: letsencrypt-prod
  hosts:
    - host: auth.example.com
      paths:
        - path: /
          pathType: Prefix
  tls:
    - secretName: pocket-id-tls
      hosts:
        - auth.example.com

persistence:
  storageClass: "standard"
  size: 1Gi

resources:
  requests:
    cpu: 50m
    memory: 128Mi
  limits:
    memory: 256Mi
```

Install the chart:

```sh
helm upgrade --install pocket-id oci://ghcr.io/norelect/charts/pocket-id \
  --namespace pocket-id \
  --values my-values.yaml
```
