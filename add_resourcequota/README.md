# Add ResourceQuota

## Install

```text
kubectl apply -f install.yaml
```

File: [install.yaml](install.yaml)

## Checks

```text
kubectl apply -f pod_without_resource_requirements.yaml

pod/nginx created

kubectl -n add-resourcequota create quota resource-quota --hard=cpu=1,memory=1G,pods=2

kubectl -n add-resourcequota delete pod nginx

kubectl apply -f pod_without_resource_requirements.yaml
Error from server (Forbidden): error when creating "pod_without_resource_requirements.yaml": pods "nginx" is forbidden: failed quota: resource-quota: must specify cpu,memory
```

## Solution

Add to yaml file under spec.containers.resource.requests with values for cpu and memory.

```text
kubectl apply -f solution.yaml
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  namespace: add-resourcequota
spec:
  containers:
  - image: nginx:1.18.0
    name: nginx
    resources:          # add this
      requests:         # add this
        cpu: "0.5"      # add this
        memory: "512"   # add this
      limits:           # add this
        cpu: "1"        # add this
        memory: "1024"  # add this
```

## Verify

```text
kubectl -n add-resourcequota describe resourcequota resource-quota

Name:       resource-quota
Namespace:  add-resourcequota
Resource    Used  Hard
--------    ----  ----
cpu         500m  1
memory      512   1G
pods        1     2

kubectl -n add-resourcequota delete pod nginx

kubectl -n add-resourcequota describe resourcequota resource-quota

Name:       resource-quota
Namespace:  add-resourcequota
Resource    Used  Hard
--------    ----  ----
cpu         0     1
memory      0     1G
pods        0     2
```
