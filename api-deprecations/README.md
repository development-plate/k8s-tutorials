# API Deprecations

## Install

```text
kubectl apply -f install.yaml
```

File: [install.yaml](install.yaml)

## Checks

```text
kubectl apply -f redis-deployment.yaml

error: resource mapping not found for name: "redis" namespace: "api-deprecations" from "redis-deployment.yaml": no matches for kind "Deployment" in version "apps/v1beta1"
ensure CRDs are installed first
```

Ok, what's wrong. We take a look on Version "apps/v1beta1".

## Solution

```text
kubectl api-versions

admissionregistration.k8s.io/v1
apiextensions.k8s.io/v1
apiregistration.k8s.io/v1
apps/v1
authentication.k8s.io/v1
authorization.k8s.io/v1
autoscaling/v1
autoscaling/v2
...
```

```yaml
```

````text
$ kubectl -n secret-and-consuming-as-env-variables delete pod nginx

$ kubectl -n secret-and-consuming-as-env-variables apply -f edit.yaml
```

## Verify

```text
kubectl -n secret-and-consuming-as-env-variables exec -it nginx -- env

PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=nginx
TERM=xterm
KEY3=TG1MSGJZaHNnV1p3TmlmaXFhUm9ySDhU # <-- Bingo!!!
```
