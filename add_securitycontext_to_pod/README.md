# Add SecurityContext to Pod

## Install

```text
kubectl apply -f install.yaml
```

File: [install.yaml](install.yaml)

## Checks

```text
kubectl -n add-securitycontext-to-pod exec -it non-root -- /bin/sh

/ # ps
PID   USER     TIME  COMMAND
    1 root      0:00 sleep 1h
    7 root      0:00 /bin/sh
   13 root      0:00 ps
```

you see a describtion without securitycontext.

## Solution

```text
cp install.yaml edit.yaml

nano edit.yaml
```

```yaml
spec:
  ...
  securityContext:    # add
    runAsUser: 1000   # add
    runAsGroup: 3000  # add
    fsGroup: 2000     # add
  containers:
  - name: secure-container
    securityContext:   # add
      allowPrivilegeEscalation: false # add
```

```text
kubectl -n add-securitycontext-to-pod delete pod non-root

kubectl -n add-securitycontext-to-pod apply -f edit.yaml
```

## Verify

```text

kubectl -n add-securitycontext-to-pod exec -it non-root -- /bin/sh

/ $ ps
PID   USER     TIME  COMMAND
    1 1000      0:00 sleep 1h
    7 1000      0:00 /bin/sh
   13 1000      0:00 ps
```
