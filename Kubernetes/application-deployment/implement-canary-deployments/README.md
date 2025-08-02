# Implement Canary Deployments

## Install

```text
kubectl apply -f install.yaml

kubectl config set-context --current --namespace=implement-canary-deployments
```

File: [install.yaml](install.yaml)

## Task

## Checks

```text
```

## Solution and Verify

```text
# kubectl create deploy old-nginx --image=nginx:1.14 --replicas=3 --dry-run=client -o yaml > oldnginx.yaml

add label "type: canary" to yaml file.

# kubectl apply -f oldnginx.yaml

deployment.apps/old-nginx created

# kubectl expose deployment old-nginx --name=oldnginx --port=80 --selector type=canary

service/oldnginx exposed

# kubectl get svc,ep -o wide

NAME               TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE     SELECTOR
service/oldnginx   ClusterIP   10.104.171.87   <none>        80/TCP    6m38s   type=canary

NAME                 ENDPOINTS                                               AGE
endpoints/oldnginx   192.168.205.207:80,192.168.35.91:80,192.168.45.254:80   6m38s

# kubectl run busybox -it --rm --image=busybox -- sh

/ # wget 10.104.171.87
Connecting to 10.104.171.87 (10.104.171.87:80)
saving to 'index.html'
index.html           100% |**********************************************************************************************************************************************|   612  0:00:00 ETA
'index.html' saved

exit the container

# kubectl cp old-nginx-66bd7bb796-grdln:/usr/share/nginx/html/index.html index.html

add a line to identifies this as the canary pod

# kubectl create configmap canary --from-file=index.html

configmap/canary created

# kubectl describe cm canary

Name:         canary
Namespace:    implement-canary-deployments
Labels:       <none>
Annotations:  <none>

Data
====
index.html:
----
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx! Canary</title>
...

# cp oldnginx.yaml canary.yaml

- change image
- replicas to 1
- change label old- to new-

# kubectl apply -f canary.yaml

deployment.apps/new-nginx created

# kubectl get svc,ep
NAME               TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
service/oldnginx   ClusterIP   10.104.171.87   <none>        80/TCP    26m

NAME                 ENDPOINTS                                               AGE
endpoints/oldnginx   192.168.205.207:80,192.168.35.91:80,192.168.45.254:80   26m

# kubectl get all
NAME                             READY   STATUS             RESTARTS      AGE
pod/new-nginx-6d7565c49b-xvhzl   0/1     CrashLoopBackOff   4 (36s ago)   2m2s
pod/old-nginx-66bd7bb796-grdln   1/1     Running            0             21m
pod/old-nginx-66bd7bb796-gscxb   1/1     Running            0             21m

# kubectl describe pod new-nginx-6d7565c49b-xvhzl

...
  Normal   Created    96s (x5 over 3m)      kubelet            Created container nginx
  Warning  Failed     96s (x5 over 3m)      kubelet            Error: failed to start container "nginx": Error response from daemon: failed to create shim: OCI runtime create failed: runc create failed: unable to start container process: error during container init: error mounting "/var/lib/kubelet/pods/517307ef-5a37-4f00-a9a5-2082870be628/volumes/kubernetes.io~configmap/canary-vol" to rootfs at "/usr/share/nginx/html/index.html": mount /var/lib/kubelet/pods/517307ef-5a37-4f00-a9a5-2082870be628/volumes/kubernetes.io~configmap/canary-vol:/usr/share/nginx/html/index.html (via /proc/self/fd/6), flags: 0x5001: not a directory: unknown: Are you trying to mount a directory onto a file (or vice-versa)? Check if the specified host path exists and is the expected type
  Normal   Pulled     96s                   kubelet            Successfully pulled image "nginx:latest" in 1.067060207s

okay, change canary.yaml file.
          # mountPath: /usr/share/nginx/html/index.html  # this runs in a error
          - mountPath: /usr/share/nginx/html/

# kubectl delete -f canary.yaml

deployment.apps "new-nginx" deleted

# kubectl apply -f canary.yaml

deployment.apps/new-nginx created

# kubectl get svc,ep
NAME               TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
service/oldnginx   ClusterIP   10.104.171.87   <none>        80/TCP    32m

NAME                 ENDPOINTS                                                            AGE
endpoints/oldnginx   192.168.205.207:80,192.168.205.221:80,192.168.35.91:80 + 1 more...   32m

# kubectl run busybox -it --rm --image=busybox -- sh

wget 10.104.171.87 && cat index.html && rm index.html

repeat this, to see sometimes the canary pod:

...
<title>Welcome to nginx! Canary</title>
...
</head>
<body>
<h1>Welcome to nginx! Canary</h1>
...

# kubectl get deploy
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
new-nginx   1/1     1            1           9m2s
old-nginx   3/3     3            3           42m

# kubectl scale deployment new-nginx --replicas=3

deployment.apps/new-nginx scaled

# kubectl scale deployment old-nginx --replicas=0

deployment.apps/old-nginx scaled

# kubectl get deploy
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
new-nginx   3/3     3            3           12m
old-nginx   0/0     0            0           45m
```
