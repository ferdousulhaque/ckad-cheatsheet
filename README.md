# CKAD Cheatsheet

Here I have listed the types of area that needs to be covered for CKAD Certification Exam.

# Master Node Services

- kubeapi
    - communicates with the pods and keeps status updated
- kube-scheduler
    - schedules the pods to the worker nodes.       
        - Filter and ranks nodes based on available resources.
        - taints and tolerations
        - node selector affinity
        - electing leader
- kube-controller-manager
    - node-controller
        - responsible for monitoring/watch the pods and remediate the situation. Monitors every 5 seconds, grace period 40s and POD eviction timeout 5m.
    - replication-controller
        - responsible for monitoring/watch the replica status
    - replica-set
    - deployment-controller
    - namespace-controller
    - service-account-controller
    - pv-binder-controller
    - job-controller
    - endpoint-controller
- etcd
    - key-value store

# Worker Node Services

- kube-proxy
    - Used for services to be accessed across the worker nodes
- kubelet
    - Register node
    - Create PODs
    - Monitor Nodes and PODs
    - Network Plugin like CNI

# YAML Format

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: nginx
    labels:
        app: testapp
        type: loadbalancer
spec:
    containers:
    - name: nginx-container
      image: nginx:alpine
```

# Daemon Sets

- kube-proxy
- weave-net

# Get All
```bash
kubectl get all
kubectl get pods -A
kubectl get pods --all-namespaces
```

# Switch Context
```bash
kubectl config set-context --current --namespace=dev
```

# Service Naming Format

`{service-name}.{namespace}.svc.cluster.local`

`db-service.dev.svc.cluster.local`

# Expose Service Ports
There are 2 ways but the second one you can not explicitly inform the label. You need to add the label by output in yaml.

## Way 01
Automatically take the selector
```bash
kubectl expose pod redis --type=NodePort --port 6379 --name redis-service
```

## Way 02
Need to specify the selector by editing the yaml
```bash
kubectl create service clusterip redis --tcp=6379:6379 --node-port=6379 --dry-run=client -o yaml
```

# Create a deployment imparative

```bash
kubectl create deploy webapp --image=nginx:alpine --replicas=3 --namespace=dev
```

## Updating a deployment

```bash
kubectl apply -f nginx.yaml
```

### Strategy

- Recreate
- RollingUpdate


# Check a rollout and history
```bash
kubectl rollout status deployment/myapp-deployment
kubectl rollout history deployment/myapp-deployment
kubectl rollout undo deployment/myapp-deployment
```

# Taints and Tolerations

## Taint Effects:

- NoSchedule
- PreferNoSchedule
- NoExecute

# Node Affinity
While creating a POD you can specify the node with specific labels

# Logs
Log exporter can be run as a side-car pattern in k8s.
```bash
kubectl logs -f {pod-name}
```

# Encode and Decode Secret

- echo –n 'mysql' | base64
- echo –n 'mysql' | base64 --decode

# Ingress Controllers

- nginx
- istio
- contour
- kong
- haproxy
- traefik