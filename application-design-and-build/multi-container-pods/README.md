# Multi-Container Pods

## Install

```text
kubectl apply -f install.yaml

kubectl config set-context --current --namespace=multi-container-pods
```

Files:

- [install.yaml](install.yaml)
- [solution.yaml](solution.yaml)

## Checks

```text
# kubectl get all -o wide --show-labels

NAME                  READY   STATUS    RESTARTS   AGE   IP               NODE        NOMINATED NODE   READINESS GATES   LABELS
pod/multi-container   2/2     Running   0          13m   192.168.35.126   kubenode2   <none>           <none>            app=multi-container
```

## Verify

```text
# kubectl describe pod multi-container

...
Containers:
  busybox:
  ...
  nginx:
  ...

# kubectl logs multi-container nginx

/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
```
