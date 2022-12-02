# Managing Jobs

## Install

```text
kubectl apply -f install.yaml

kubectl config set-context --current --namespace=managing-jobs
```

File: [install.yaml](install.yaml)

## Task

Create a job myjob with image busybox and 'sleep 5' command. Job should have completions=3 and ttlSecondsAfterFinished=60.

## Checks

```text
kubectl get jobs,pods

No resources found in managing-jobs namespace.
```

## Solution

```text
kubectl create job myjob --image=busybox --dry-run=client -o yaml -- sleep 5 > myjob.yaml
```

```yaml
...
spec:
  completions: 3              #add this
  ttlSecondsAfterFinished: 60 # add this
...
```

````text
kubectl apply -f myjob.yaml
```

## Verify

```text
kubectl get jobs,pods
NAME              COMPLETIONS   DURATION   AGE
job.batch/myjob   1/3           19s        19s

NAME              READY   STATUS      RESTARTS   AGE
pod/myjob-854tb   0/1     Completed   0          19s
pod/myjob-mzpk6   0/1     Completed   0          9s


# much later

kubectl get jobs,pods
No resources found in managing-jobs namespace.
```
