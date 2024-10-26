# Storage Class - Persistent Volume - Persistent Volume Claim - POD

## Storage Class
```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: fast-storage
provisioner: kubernetes.io/no-provisioner
volumeBindingMode: Immediate
```

## Persistent Volume
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: fast-pv-cka
spec:
  capacity:
    storage: 50Mi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  storageClassName: fast-storage
  hostPath:
    path: "/tmp/fast-data"
```

## Persistent Volume Claim
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: fast-pvc-cka
spec:
  storageClassName: fast-storage
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 30Mi
```

## Pod
```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: fast-pod-cka
  name: fast-pod-cka
spec:
  containers:
  - image: nginx:latest
    name: fast-pod-cka
    resources: {}
    volumeMounts:
    - mountPath: "/app/data"
      name: task-pv-storage
  dnsPolicy: ClusterFirst
  restartPolicy: Always
  volumes:
    - name: task-pv-storage
      persistentVolumeClaim:
        claimName: fast-pvc-cka
status: {}
```