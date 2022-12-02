# Services Networking > Creating Services

## Install

```text
kubectl apply -f install.yaml

kubectl config set-context --current --namespace=creating-services
```

File: [install.yaml](install.yaml)

## Task

Creating a service

## Checks

```text
kubectl get all

No resources found in creating-services namespace.
```

## Solution and Verify

```text
# kubectl create deployment nginxsvc --image=nginx

deployment.apps/nginxsvc created

# kubectl scale deployment nginxsvc --replicas=3

deployment.apps/nginxsvc scaled

# kubectl expose deployment nginxsvc --port=80

service/nginxsvc exposed

# kubectl describe svc nginxsvc

Name:              nginxsvc
Namespace:         creating-services
Labels:            app=nginxsvc
Annotations:       <none>
Selector:          app=nginxsvc
Type:              ClusterIP
IP Family Policy:  SingleStack
IP Families:       IPv4
IP:                10.109.60.2
IPs:               10.109.60.2
Port:              <unset>  80/TCP
TargetPort:        80/TCP
Endpoints:         192.168.205.242:80,192.168.35.90:80,192.168.45.236:80      # <-- look here. IP Address of Pods
Session Affinity:  None
Events:            <none>

# kubectl get svc nginxsvc -o=yaml

# kubectl get svc,ep

NAME               TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)   AGE
service/nginxsvc   ClusterIP   10.109.60.2   <none>        80/TCP    3m5s

NAME                 ENDPOINTS                                               AGE
endpoints/nginxsvc   192.168.205.242:80,192.168.35.90:80,192.168.45.236:80   3m5s

# kubectl exec -it nginxsvc-6f45cc47b4-crpck -- sh

And run inside of container:

# curl 192.168.205.242:80

<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
...
</html>

# curl 10.109.60.2:80

<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
...
</html>

Leave container
# exit

kubectl edit svc nginxsvc
```

```yaml
spec:
  clusterIP: 10.109.60.2
  clusterIPs:
  - 10.109.60.2
  externalTrafficPolicy: Cluster
  internalTrafficPolicy: Cluster
  ipFamilies:
  - IPv4
  ipFamilyPolicy: SingleStack
  ports:
  - nodePort: 32000        # add nodePort: 32000
    port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: nginxsvc
  sessionAffinity: None
  type: NodePort           # change from ClusterIP to NodePort
```

```text
# kubectl get svc,ep

NAME               TYPE       CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
service/nginxsvc   NodePort   10.109.60.2   <none>        80:32000/TCP   17m

NAME                 ENDPOINTS                                               AGE
endpoints/nginxsvc   192.168.205.242:80,192.168.35.90:80,192.168.45.236:80   17m

# kubectl get pod -o wide

NAME                        READY   STATUS    RESTARTS   AGE   IP                NODE        NOMINATED NODE   READINESS GATES
nginxsvc-6f45cc47b4-crpck   1/1     Running   0          21m   192.168.205.242   kubenode1   <none>           <none>
nginxsvc-6f45cc47b4-hgx64   1/1     Running   0          22m   192.168.35.90     kubenode2   <none>           <none>
nginxsvc-6f45cc47b4-hmrk2   1/1     Running   0          21m   192.168.45.236    kubenode3   <none>           <none>

# curl kubenode1:32000

<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
...
</html>
```
