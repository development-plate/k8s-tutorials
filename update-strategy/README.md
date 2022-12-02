# Update Strategy

## Install

```text
kubectl apply -f install.yaml

kubectl config set-context --current --namespace=update-strategy
```

File: [install.yaml](install.yaml)

## Task

We see a rolling update strategy in action.

## Checks

```text
kubectl get deploy bluelabel -o yaml | grep -4 strategy:

  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app: bluelabel
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
```

## Solution and Verify

```text
kubectl scale deploy bluelabel --replicas=4

deployment.apps/bluelabel scaled

kubectl get all --selector app=bluelabel

NAME                             READY   STATUS    RESTARTS   AGE
pod/bluelabel-5996d849cb-5mvn5   1/1     Running   0          4s
pod/bluelabel-5996d849cb-bhw56   1/1     Running   0          20s
pod/bluelabel-5996d849cb-k7pm7   1/1     Running   0          4s
pod/bluelabel-5996d849cb-kvl65   1/1     Running   0          4s

NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/bluelabel   4/4     4            4           20s

NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/bluelabel-5996d849cb   4         4         4       20s

kubectl set env deploy bluelabel type=blended

deployment.apps/bluelabel env updated

kubectl get all --selector app=bluelabel

NAME                             READY   STATUS              RESTARTS   AGE
pod/bluelabel-5996d849cb-5mvn5   1/1     Running             0          28s
pod/bluelabel-5996d849cb-bhw56   1/1     Running             0          44s
pod/bluelabel-5996d849cb-k7pm7   1/1     Running             0          28s
pod/bluelabel-759cdb6f4-95ptl    0/1     ContainerCreating   0          1s
pod/bluelabel-759cdb6f4-hnqwc    0/1     ContainerCreating   0          1s

NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/bluelabel   3/4     2            3           44s

NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/bluelabel-5996d849cb   3         3         3       44s
replicaset.apps/bluelabel-759cdb6f4    2         2         0       1s


kubectl set image deployment bluelabel nginx=nginx:1.14.0

deployment.apps/bluelabel image updated

kubectl get all --selector app=bluelabel
NAME                             READY   STATUS              RESTARTS   AGE
pod/bluelabel-65bcfbc7db-dzpbj   0/1     ContainerCreating   0          2s
pod/bluelabel-65bcfbc7db-zmj6w   0/1     ContainerCreating   0          2s
pod/bluelabel-759cdb6f4-8gg9n    1/1     Running             0          105s
pod/bluelabel-759cdb6f4-hnqwc    1/1     Running             0          108s
pod/bluelabel-759cdb6f4-qqcg8    1/1     Running             0          105s

NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/bluelabel   3/4     2            3           2m31s

NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/bluelabel-5996d849cb   0         0         0       2m31s
replicaset.apps/bluelabel-65bcfbc7db   2         2         0       2s
replicaset.apps/bluelabel-759cdb6f4    3         3         3       108s

kubectl rollout history deployment bluelabel

deployment.apps/bluelabel
REVISION  CHANGE-CAUSE
1         <none>
2         <none>
3         <none>

kubectl rollout undo deployment bluelabel --to-revision=2
deployment.apps/bluelabel rolled back

kubectl get all --selector app=bluelabel
NAME                            READY   STATUS    RESTARTS   AGE
pod/bluelabel-759cdb6f4-8tspk   1/1     Running   0          5s
pod/bluelabel-759cdb6f4-fp8br   1/1     Running   0          7s
pod/bluelabel-759cdb6f4-qrvmz   1/1     Running   0          7s
pod/bluelabel-759cdb6f4-vjq2s   1/1     Running   0          5s

NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/bluelabel   4/4     4            4           4m50s

NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/bluelabel-5996d849cb   0         0         0       4m50s
replicaset.apps/bluelabel-65bcfbc7db   0         0         0       2m21s
replicaset.apps/bluelabel-759cdb6f4    4         4         4       4m7s

kubectl rollout history deployment bluelabel

deployment.apps/bluelabel
REVISION  CHANGE-CAUSE
1         <none>
3         <none>
4         <none>
```
