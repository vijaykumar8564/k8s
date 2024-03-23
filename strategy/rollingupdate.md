* strategy (RollingUpdate)
* In Kubernetes, "maxSurge" is a parameter used in rolling update strategies for Deployments and ReplicaSets. It specifies the maximum number of additional Pods that can be created above the desired number of replicas during a rolling update.
* In Kubernetes, "maxUnavailable" is a parameter used in rolling update strategies for Deployments and ReplicaSets. It specifies the maximum number or percentage of Pods that can be unavailable (i.e., unavailable due to updates or failures) during a rolling update process.


```python
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
  namespace: dev
  labels:
    env: dev
spec:
  replicas: 10
  selector:
    matchLabels:
      app: my-app
  strategy:
        rollingUpdate: 
          maxSurge: 25%
          maxUnavailable: 25%
        type: RollingUpdate
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-container
        image: nginx
        ports:
        - containerPort: 80
      nodeSelector:
        env: dev
```

* change the container image with below command

```python
kubectl set image deployment/my-app my-container=nginx:1.24
```

* use case
- Once all new Pods are successfully deployed and become ready, and the old Pods are terminated, the rolling update process is considered complete.
