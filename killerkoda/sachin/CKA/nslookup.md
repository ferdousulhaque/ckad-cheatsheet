# Question

For this question, please set this context (In exam, diff cluster name)

kubectl config use-context kubernetes-admin@kubernetes


Create an nginx pod named nginx-pod-cka using the nginx image, and expose it internally with a service named nginx-service-cka . Verify your ability to perform DNS lookups for the service name from within the cluster using the busybox image. Record the results in nginx-service.txt .

## Solution

```bash
k run nginx-pod-cka --image=nginx
k expose pod nginx-pod-cka --name=nginx-service-cka --port=80
k run busybox --image=busybox -- sleep 3600
k exec busybox -- nslookup nginx-service-cka.default.svc.cluster.local > nginx-service.txt
```