# Add ServiceAccount to Deployment

## Install

```text
kubectl apply -f install.yaml
```

File: [install.yaml](install.yaml)

## Checks

```text
# kubectl -n add-sa-to-deployment get pods

NAME                    READY   STATUS    RESTARTS   AGE
nginx-7c969dc4f-knrmm   1/1     Running   0          71s

$ kubectl -n add-sa-to-deployment describe pod nginx-7c969dc4f-knrmm | grep 'Service Account'

Service Account:  default
```

Ok, that's wrong. We have already installed a SA with name 'deploy-sa'.

## Solution 1

Use kubectl set serviceaccount command. See [more](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-serviceaccount-em--1).

```text
kubectl -n add-sa-to-deployment set serviceaccount deployment nginx deploy-sa

deployment.apps/nginx serviceaccount updated


```

## Solution 2

```text
$ kubectl -n add-sa-to-deployment get deployment nginx
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   1/1     1            1           4m45s

$ kubectl -n add-sa-to-deployment edit deployment nginx
```

```yaml
spec:
  ...
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: nginx
    spec:
      serviceAccount: deploy-sa  # <-- add this 
      containers:
      - image: nginx:1.14.0
```

## Verify

```text
kubectl -n add-sa-to-deployment describe pod nginx-7c969dc4f-knrmm | grep 'Service Account'

Service Account:  deploy-sa
```
