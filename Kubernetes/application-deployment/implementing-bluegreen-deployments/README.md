# Implementing Blue/Green Deployments

## Install

```text
kubectl apply -f install.yaml

kubectl config set-context --current --namespace=implementing-bluegreen-deployments
```

File: [install.yaml](install.yaml)

## Task

## Checks

```text
```

## Solution and Verify

```text
# kubectl apply -f blue-nginx.yaml

deployment.apps/blue-nginx created

# kubectl expose deployment blue-nginx --port=80 --name=bgnginx

service/bgnginx exposed

# cp blue-nginx.yaml green-nginx.yaml

- change image version
- change blue to green

# kubectl apply -f green-nginx.yaml

deployment.apps/green-nginx created

# kubectl get pods
NAME                           READY   STATUS    RESTARTS   AGE
blue-nginx-848595fbc5-2p9hd    1/1     Running   0          4m36s
blue-nginx-848595fbc5-hmxf6    1/1     Running   0          4m36s
blue-nginx-848595fbc5-kqmmc    1/1     Running   0          4m36s
green-nginx-6cd6cb7999-788q6   1/1     Running   0          42s
green-nginx-6cd6cb7999-r24z7   1/1     Running   0          42s
green-nginx-6cd6cb7999-skftc   1/1     Running   0          42s

# kubectl delete svc bgnginx; kubectl expose deployment green-nginx --port=80 --name=bgnginx


```
