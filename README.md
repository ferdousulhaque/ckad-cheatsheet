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

# Create Deployment Yaml

```bash
kubectl create deployment app --image=nginx:alpine --dry-run=client -o yaml > deploy.yaml
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
kubectl config use-context prod-user@production
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

# Operating system update / kubeadm cluster upgrade

## First remove the node from scheduling
```
kubectl drain node1
kubectl cordon node1
```

## upgrade plan

```
kubeadm upgrade plan
kubeadm upgrade apply
```
## Component upgrade
```
apt-get upgrade -y kubelet=1.12.0-0.0
```
## After activity
```
kubectl uncordon node1
```
# ETCD Snapshot Backup
```
ETCDCTL_API=3 etcdctl snapshot save snapshot.db
```
# Certificate Generate and View

- Generate Key file [KEY]
```
openssl genrsa -out admin.key 2048

```
- Certificate Signing Request [CSR]
```
openssl req -new -key jane.key -subj "/CN=jane" -out jane.csr

```
- Self Signed Certificate [CRT]
```
openssl x509 -req -in admin.csr -CA ca.crt -signkey admin.key -out admin.crt 

```
- View a CRT file
```
openssl x509 -in ~/admin.crt -text -noout
```

# Certificate Signing Request YAML

```yaml
apiVersion: certificates.k8s.io/v1beta1
kind: CertificateSigningRequest
metadata:
name: jane
spec:
groups:
- system:authenticated
usages:
- digital signature
- key encipherment
- server auth
request:
```

# Get list of CSR and Approval

```
kubectl get csr
kubectl certificate approve jane
kubectl get csr jane -o yaml
```

```yaml
apiVersion: v1
kind: Config
clusters:
contexts:
users:
-   name: my-kube-playground
    cluster:
    certificate-authority:
    server: https://my-kube-playground:6443
-   name: my-kube-admin@my-kube-playground
    context:
    cluster:
    user:
-   name: my-kube-admin
    user:
    client-certificate:
    client-key:
        ca.crt
        admin.crt
        admin.key
```

# Viewing a config file

```bash
kubectl config view
```

# API Segregation

```shell
+---/api
|   |   
|   \---v1
        |
        \---namespaces
            events
            bindings
            configmaps
            pods
            endpoints
            PV
            secrets
            rc
            nodes
            PVC
            services         
```

```shell
+---/apis
|   |   
|   \---v1
        |
        \---namespaces
            events
            bindings
            configmaps
            pods
            endpoints
            PV
            secrets
            rc
            nodes
            PVC
            services         
```


# Kubectl Proxy
If we enable it then no need to put key and crt file to call the kubeapi APIs

```bash
kubectl proxy
```

# Verbs

- list
- get
- create
- delete
- update
- watch

# Inspect Service Logs

```bash
journalctl -u etcd.service -l
```

# Login to private registry

```bash
docker login private-registry.io
```

# Ingress Controllers

- nginx
- istio
- contour
- kong
- haproxy
- traefik

# RBAC - Role Based Authorization Control
There are 4 different RBAC combinations and 3 valid ones:

- Role + RoleBinding (available in single Namespace, applied in single Namespace)
- ClusterRole + ClusterRoleBinding (available cluster-wide, applied cluster-wide)
- ClusterRole + RoleBinding (available cluster-wide, applied in single Namespace)
- Role + ClusterRoleBinding (NOT POSSIBLE: available in single Namespace, applied cluster-wide)