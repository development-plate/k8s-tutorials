# Add ResourceQuota 2

## Install

```text
kubectl apply -f install.yaml

output:
namespace/add-resourcequota2 created
```

File: [install.yaml](install.yaml)

## Set context to namespace

```text
kubectl config set-context --namespace=add-resourcequota2 --cur
rent

output
Context "kubernetes-admin@kubernetes" modified.
```

## Set Quota

```text
kubectl create quota qtest --hard pods=3,cpu=100m,memory=500Mi

output:
resourcequota/qtest created
```

## Create deployment

```text
kubectl create deploy nginx --image=nginx --replicas=3

deployment.apps/nginx created
```


## Checks

```text
kubectl get all

output:
NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx   0/3     0            0           3m50s

NAME                               DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-7854ff8877   3         0         0       3m50s
```

You see no pods here. What is the reason?

```text
# check replicaset

kubectl describe rs nginx-7854ff8877

output:
...
Conditions:
  Type             Status  Reason
  ----             ------  ------
  ReplicaFailure   True    FailedCreate
Events:
  Type     Reason        Age               From                   Message
  ----     ------        ----              ----                   -------
  Warning  FailedCreate  6m2s              replicaset-controller  Error creating: pods "nginx-7854ff8877-rnkdt" is forbidden: failed quota: qtest: must specify cpu for: nginx; memory for: nginx
  Warning  FailedCreate  6m2s              replicaset-controller  Error creating: pods "nginx-7854ff8877-9nsgs" is forbidden: failed quota: qtest: must specify cpu for: nginx; memory for: nginx
  Warning  FailedCreate  6m2s              replicaset-controller  Error creating: pods "nginx-7854ff8877-4qsmn" is forbidden: failed quota: qtest: must specify cpu for: nginx; memory for: nginx
  Warning  FailedCreate  6m2s              replicaset-controller  Error creating: pods "nginx-7854ff8877-7g7n5" is forbidden: failed quota: qtest: must specify cpu for: nginx; memory for: nginx
  Warning  FailedCreate  6m2s              replicaset-controller  Error creating: pods "nginx-7854ff8877-llcps" is forbidden: failed quota: qtest: must specify cpu for: nginx; memory for: nginx
  Warning  FailedCreate  6m2s              replicaset-controller  Error creating: pods "nginx-7854ff8877-nbr9p" is forbidden: failed quota: qtest: must specify cpu for: nginx; memory for: nginx
  Warning  FailedCreate  6m2s              replicaset-controller  Error creating: pods "nginx-7854ff8877-srcg2" is forbidden: failed quota: qtest: must specify cpu for: nginx; memory for: nginx
  Warning  FailedCreate  6m1s              replicaset-controller  Error creating: pods "nginx-7854ff8877-9556m" is forbidden: failed quota: qtest: must specify cpu for: nginx; memory for: nginx
  Warning  FailedCreate  6m1s              replicaset-controller  Error creating: pods "nginx-7854ff8877-rpd2x" is forbidden: failed quota: qtest: must specify cpu for: nginx; memory for: nginx
  Warning  FailedCreate  34s (x8 over 6m)  replicaset-controller  (combined from similar events): Error creating: pods "nginx-7854ff8877-ps57q" is forbidden: failed quota: qtest: must specify cpu for: nginx; memory for: nginx
```

## Solution

You must specify the cpu and memory for nginx.

```text
kubectl set resources deploy nginx --requests cpu=100m,memory=5Mi --limits cpu=200m,memory=20Mi

output:
deployment.apps/nginx resource requirements updated
```

## Verify

```text
kubectl get all

output:
NAME                        READY   STATUS    RESTARTS   AGE
pod/nginx-5c68f7c87-r9f4r   1/1     Running   0          29s

NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx   1/3     1            1           10m

NAME                               DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-5c68f7c87    2         1         1       29s
replicaset.apps/nginx-7854ff8877   2         0         0       10m
```

You see only one pod and not three. Check the Quota with this command.

```text
kubectl describe ns add-resourcequota2

output:
Name:         add-resourcequota2
Labels:       kubernetes.io/metadata.name=add-resourcequota2
Annotations:  <none>
Status:       Active

Resource Quotas
  Name:     qtest
  Resource  Used  Hard
  --------  ---   ---
  cpu       100m  100m
  memory    5Mi   500Mi
  pods      1     3

No LimitRange resource.
```