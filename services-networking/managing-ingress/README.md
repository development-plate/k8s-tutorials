# Services Networking > Managing Ingress

## Install

```text
kubectl apply -f install.yaml

kubectl config set-context --current --namespace=managing-ingress
```

Files:

- [install.yaml](install.yaml)
- [ingress.yaml](ingress.yaml)

## Task

Creating a Ingress

## Checks

```text
# kubectl get all

NAME                            READY   STATUS    RESTARTS   AGE
pod/nginxsvc-5b5649fd6f-5wf4n   1/1     Running   0          48s
pod/nginxsvc-5b5649fd6f-86sj2   1/1     Running   0          48s
pod/nginxsvc-5b5649fd6f-rwg9x   1/1     Running   0          48s

NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginxsvc   3/3     3            3           48s

NAME                                  DESIRED   CURRENT   READY   AGE
replicaset.apps/nginxsvc-5b5649fd6f   3         3         3       48s
```

## Solution and Verify


ingress.yaml

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: nginxsvc-ingress
  namespace: managing-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
  - http:
      paths:
      - path: /*
        pathType: Prefix
        backend:
          service:
            name: nginxsvc
            port:
              number: 80
```

```text
# kubectl expose deployment nginxsvc --port=80 --type=NodePort

service/nginxsvc exposed

# kubectl get all -o wide

NAME                            READY   STATUS    RESTARTS   AGE    IP                NODE        NOMINATED NODE   READINESS GATES
pod/nginxsvc-5b5649fd6f-2bpks   1/1     Running   0          4m3s   192.168.205.245   kubenode1   <none>           <none>
pod/nginxsvc-5b5649fd6f-5hnb9   1/1     Running   0          4m3s   192.168.35.74     kubenode2   <none>           <none>
pod/nginxsvc-5b5649fd6f-v4ds4   1/1     Running   0          4m3s   192.168.45.227    kubenode3   <none>           <none>

NAME               TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE   SELECTOR
service/nginxsvc   NodePort   10.104.117.0   <none>        80:30607/TCP   81s   app=nginxsvc

NAME                       READY   UP-TO-DATE   AVAILABLE   AGE    CONTAINERS   IMAGES   SELECTOR
deployment.apps/nginxsvc   3/3     3            3           4m3s   nginx        nginx    app=nginxsvc

NAME                                  DESIRED   CURRENT   READY   AGE    CONTAINERS   IMAGES   SELECTOR
replicaset.apps/nginxsvc-5b5649fd6f   3         3         3       4m3s   nginx        nginx    app=nginxsvc,pod-template-hash=5b5649fd6f

# kubectl apply -f ingress.yaml
ingress.networking.k8s.io/nginxsvc-ingress configured

curl kubemaster:30607
```
