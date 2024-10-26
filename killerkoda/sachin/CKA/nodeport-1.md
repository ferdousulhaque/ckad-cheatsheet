# NodePort 1


## Solution

```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: my-web-app-service
  name: my-web-app-service
  namespace: default
spec:
  ports:
  - nodePort: 30770
    port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: my-web-app-deployment
  sessionAffinity: None
  type: NodePort
status:
  loadBalancer: {}
```