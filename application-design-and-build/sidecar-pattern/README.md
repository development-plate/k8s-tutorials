# Sidecar Pattern

## Install

```text
kubectl apply -f install.yaml

kubectl config set-context --current --namespace=sidecar-pattern
```

Files:

- [install.yaml](install.yaml)

## Checks

```text
# kubectl get all -o wide --show-labels

NAME                  READY   STATUS    RESTARTS   AGE   IP               NODE        NOMINATED NODE   READINESS GATES   LABELS
pod/multi-container   2/2     Running   0          13m   192.168.35.126   kubenode2   <none>           <none>            app=multi-container
```

## Verify

```text
# kubectl expose pod sidecar-pod --port=80 --type=NodePort

service/sidecar-pod exposed

# kubectl get pod sidecar-pod -o yaml | grep hostIP

hostIP: 192.168.0.153

# kubectl get all -o wide --show-labels

NAME              READY   STATUS    RESTARTS   AGE   IP               NODE        NOMINATED NODE   READINESS GATES   LABELS
pod/sidecar-pod   2/2     Running   0          17m   192.168.45.198   kubenode3   <none>           <none>            app=sidecar-pattern

NAME                  TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE   SELECTOR              LABELS
service/sidecar-pod   NodePort   10.105.197.65   <none>        80:32583/TCP   3s    app=sidecar-pattern   app=sidecar-pattern

# curl 192.168.0.153:32583/date.txt
Sat Dec  3 15:16:05 UTC 2022
Sat Dec  3 15:16:15 UTC 2022
Sat Dec  3 15:16:25 UTC 2022
```
