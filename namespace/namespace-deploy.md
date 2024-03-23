* create namespace
```python
kubectl create namespace dev
```

* list the namespaces
```python
kubectl get ns
```

* create deployment in namespace

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
      containers:
      - name: my-container
        image: nginx
        ports:
        - containerPort: 80
      nodeSelector:
        env: dev
```
* To check the pods running status
```python 
kubectl get po -n dev
```
* you can set the current context to namespace

```python
kubectl config set-context --current --namespace=dev
```
