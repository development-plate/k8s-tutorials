# Create storage

## Install

```text
kubectl apply -f install.yaml
```

File: [install.yaml](install.yaml)

### Set context to namespace

```text
kubectl config set-context --current --namespace=create-storage
```
```text
output
Context "kubernetes-admin@kubernetes" modified.
```

## Checks

```text
```

## Solution

```text
kubectl apply -f pvol.yaml
```

```yaml
 apiVersion: v1
 kind: PersistentVolume
 metadata:
   name: pvvol-1
 spec:
   capacity:
     storage: 1Gi
   accessModes:
     - ReadWriteMany
   persistentVolumeReclaimPolicy: Retain
   nfs:
     path: /export/nfs-storage #<-- Edit to NFS export
     server: vm-nfs-server #<-- Edit to NFS server 
     readOnly: false
```

```text
kubectl apply -f pvc.yaml
```

```yaml
 apiVersion: v1
 kind: PersistentVolumeClaim
 metadata:
   name: pvc-one
 spec:
   accessModes:
   - ReadWriteMany
   resources:
     requests:
       storage: 200Mi
```

```text
kubectl apply -f nfs-pod.yaml
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
   annotations:
     deployment.kubernetes.io/revision: "1"
   generation: 1
   labels:
     run: nginx
   name: nginx-nfs #<-- Edit name
spec:
   replicas: 1
   selector:
     matchLabels:
       run: nginx
   strategy:
       rollingUpdate:
         maxSurge: 1
         maxUnavailable: 1
       type: RollingUpdate
   template:
       metadata:
         creationTimestamp: null
         labels:
           run: nginx
       spec:
         containers:
         - image: nginx
           imagePullPolicy: Always
           name: nginx
           volumeMounts: #<-- Add these three lines
           - name: nfs-vol
             mountPath: /opt
           ports:
           - containerPort: 80
             protocol: TCP
           resources: {}
           terminationMessagePath: /dev/termination-log
           terminationMessagePolicy: File
         volumes: #<-- Add these four lines
         - name: nfs-vol
           persistentVolumeClaim:
             claimName: pvc-one
         dnsPolicy: ClusterFirst
         restartPolicy: Always
         schedulerName: default-scheduler
         securityContext: {}
         terminationGracePeriodSeconds: 30
```

## Verify

```text
kubectl get pv,pvc,po
```

```text
output

NAME                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                    STORAGECLASS   REASON   AGE
persistentvolume/pvvol-1   1Gi        RWX            Retain           Bound    create-storage/pvc-one                           46m

NAME                            STATUS   VOLUME    CAPACITY   ACCESS MODES   STORAGECLASS   AGE
persistentvolumeclaim/pvc-one   Bound    pvvol-1   1Gi        RWX                           18m

NAME                             READY   STATUS    RESTARTS   AGE
pod/nginx-nfs-68686f6d59-qn55c   1/1     Running   0          3m18s
```
