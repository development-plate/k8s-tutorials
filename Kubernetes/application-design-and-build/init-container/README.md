# Init Containers

## Install

```text
kubectl apply -f install.yaml

kubectl config set-context --current --namespace=init-containers
```

Files:

- [install.yaml](install.yaml)

## Checks

```text
# kubectl get all -o wide --show-labels

```

## Verify

```text
# kubectl get all -o wide --show-labels

NAME                      READY   STATUS            RESTARTS   AGE   IP              NODE        NOMINATED NODE   READINESS GATES   LABELS
pod/init-containers-pod   0/1     PodInitializing   0          3s    192.168.35.96   kubenode2   <none>           <none>            app=init-containers-pod

# kubectl get all -o wide --show-labels

NAME                      READY   STATUS    RESTARTS   AGE   IP              NODE        NOMINATED NODE   READINESS GATES   LABELS
pod/init-containers-pod   1/1     Running   0          5s    192.168.35.96   kubenode2   <none>           <none>            app=init-containers-pod

# kubectl expose pod init-containers-pod --port 80 --type=NodePort

service/init-containers-pod exposed

# kubectl get pod init-containers-pod -o yaml | grep hostIP

hostIP: 192.168.0.152

# kubectl get all -o wide --show-labels

NAME                      READY   STATUS    RESTARTS   AGE     IP              NODE        NOMINATED NODE   READINESS GATES   LABELS
pod/init-containers-pod   1/1     Running   0          2m43s   192.168.35.96   kubenode2   <none>           <none>            app=init-containers-pod

NAME                          TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE     SELECTOR                  LABELS
service/init-containers-pod   NodePort   10.106.241.16   <none>        80:31012/TCP   2m27s   app=init-containers-pod   app=init-containers-pod

# curl 192.168.0.152:31012
Hello World!

```
