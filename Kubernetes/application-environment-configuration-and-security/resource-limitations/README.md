# Add Resource Limitations

## Install

```text
kubectl apply -f install.yaml

kubectl config set-context --current --namespace=resource-limitations
```

Files:

- [install.yaml](install.yaml)
- [solution.yaml](solution.yaml)

## Checks

```text
# kubectl get all

NAME                        READY   STATUS    RESTARTS   AGE
pod/nginx-7c969dc4f-6qthw   1/1     Running   0          5s

NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx   1/1     1            1           5s

NAME                              DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-7c969dc4f   1         1         1       5s

# kubectl describe pod nginx-7c969dc4f-6qthw | grep -4 Requests:
```

## Solution 1

```text
kubectl set resources deployment nginx --limits=cpu=200m,memory=512Mi --requests=cpu=100m,memory=256Mi

deployment.apps/nginx resource requirements updated
```

## Solution 2

```yaml
...
        ports:
        - containerPort: 80
        resources:  # Add this
          requests:
            cpu: "100m"
            memory: "256Mi"
          limits:
            cpu: "200m"
            memory: "512Mi"
```

## Verify

```text
# kubectl rollout history deployment nginx

deployment.apps/nginx
REVISION  CHANGE-CAUSE
1         <none>
2         <none>

# kubectl describe pod nginx-7844c97fff-2jlmj | grep -4 Requests:

    Restart Count:  0
    Limits:
      cpu:     200m
      memory:  512Mi
    Requests:
      cpu:        100m
      memory:     256Mi
    Environment:  <none>
    Mounts:
```