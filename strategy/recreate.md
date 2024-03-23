* Recreate


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
    type: Recreate
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
* you can see the pods recreating in other terminal 
```python
watch kubectl get po
```
* use case

- The Recreate strategy is useful in certain scenarios where a brief period of downtime is acceptable, or when you want to ensure that all Pods are replaced simultaneously.
- However, for most use cases, the default rolling update strategy (RollingUpdate) is preferred because it allows for zero-downtime updates by gradually replacing Pods with new ones.
