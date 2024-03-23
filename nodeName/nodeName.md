* nodeName you can directly specify the node name
```python
kubectl get nodes
```
 * use anynode name to deploy the pods
 
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
  template:
    metadata:
      labels:
        app: my-app
    spec:
      nodeName: node
      containers:
      - name: my-container
        image: nginx
        ports:
        - containerPort: 80
```
