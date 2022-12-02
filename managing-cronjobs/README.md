# Managing Cronjobs

## Install

```text
kubectl apply -f install.yaml

kubectl config set-context --current --namespace=managing-cronjobs
```

File: [install.yaml](install.yaml)

## Task

Create a cron job mycronjob with image busybox and echo 'greetings from your cluster' command. Job should runs every minutes.

## Checks

```text
kubectl get cronjobs,jobs,pods

No resources found in managing-cronjobs namespace.
```

## Solution

```text
kubectl create cronjob mycronjob --image=busybox --schedule="*/1 * * * *" --dry-run=client -o yaml -- echo 'greetings from your cluster' > mycronjob.yaml
```

```text
kubectl apply -f mycronjob.yaml
```

## Verify

```text
kubectl get cronjobs,jobs,pods

NAME                      SCHEDULE      SUSPEND   ACTIVE   LAST SCHEDULE   AGE
cronjob.batch/mycronjob   */1 * * * *   False     0        <none>          4s

much later

kubectl get cronjobs,jobs,pods
NAME                      SCHEDULE      SUSPEND   ACTIVE   LAST SCHEDULE   AGE
cronjob.batch/mycronjob   */1 * * * *   False     0        30s             38s

NAME                           COMPLETIONS   DURATION   AGE
job.batch/mycronjob-27833102   1/1           3s         30s

NAME                           READY   STATUS      RESTARTS   AGE
pod/mycronjob-27833102-wh4mj   0/1     Completed   0          30s

kubectl logs mycronjob-27833102-wh4mj
greetings from your cluster

```
