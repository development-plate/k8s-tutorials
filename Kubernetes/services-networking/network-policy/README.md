# Services Networking > Network Policy

## Install

```text
kubectl apply -f install.yaml

kubectl config set-context --current --namespace=network-policy
```

Files:

- [install.yaml](install.yaml)
- [network-policy.yaml](network-policy.yaml)

## Task

Creating a Network Policy from busybox to nginx.

## Checks

```text
# kubectl get all -o wide --show-labels

NAME          READY   STATUS    RESTARTS   AGE   IP                NODE        NOMINATED NODE   READINESS GATES   LABELS
pod/busybox   1/1     Running   0          14m   192.168.205.210   kubenode1   <none>           <none>            app=sleepy
pod/nginx     1/1     Running   0          14m   192.168.35.114    kubenode2   <none>           <none>            app=nginx
```

## Solution and Verify

network-policy.yaml

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: access-nginx
spec:
  podSelector:
    matchLabels:
      app: nginx
  ingress:
  - from:
    - podSelector:
        matchLabels:
          access: "true"
```

```text
# kubectl apply -f network-policy.yaml

networkpolicy.networking.k8s.io/access-nginx created

! hint ip of pod nginx: 192.168.35.114
# kubectl exec -it busybox -- wget --timeout=1 192.168.35.114

Connecting to 192.168.35.114 (192.168.35.114:80)
wget: download timed out
command terminated with exit code 1

# kubectl label pod busybox access=true

pod/busybox labeled

# kubectl exec -it busybox -- wget --timeout=1 192.168.35.114

Connecting to 192.168.35.114 (192.168.35.114:80)
saving to 'index.html'
index.html           100% |********************************|   612  0:00:00 ETA
'index.html' saved
```
