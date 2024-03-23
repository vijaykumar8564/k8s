* check the node labels

```python
kubectl get node node --show-labels
```

* set label to the node
```python
kubectl label node node env=dev
```

* unset the label to the node
```python
kubectl label node node env=dev-
```

* Lets run deploy with nodeSelector

```python
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
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

* all pods are deployed in matched label node

```python
NAME                     READY   STATUS    RESTARTS   AGE     IP            NODE   NOMINATED NODE   READINESS GATES
my-app-cd8d494c7-2qr5r   1/1     Running   0          6m28s   10.244.1.32   node   <none>           <none>
my-app-cd8d494c7-9qp6q   1/1     Running   0          6m28s   10.244.1.38   node   <none>           <none>
my-app-cd8d494c7-ddfx7   1/1     Running   0          6m28s   10.244.1.31   node   <none>           <none>
my-app-cd8d494c7-ghnf5   1/1     Running   0          6m28s   10.244.1.37   node   <none>           <none>
my-app-cd8d494c7-lxznt   1/1     Running   0          6m28s   10.244.1.33   node   <none>           <none>
my-app-cd8d494c7-nmbkr   1/1     Running   0          6m28s   10.244.1.34   node   <none>           <none>
my-app-cd8d494c7-p4tnf   1/1     Running   0          6m28s   10.244.1.35   node   <none>           <none>
my-app-cd8d494c7-r4887   1/1     Running   0          6m28s   10.244.1.36   node   <none>           <none>
my-app-cd8d494c7-v6xv9   1/1     Running   0          6m28s   10.244.1.40   node   <none>           <none>
my-app-cd8d494c7-zns7z   1/1     Running   0          6m28s   10.244.1.39   node   <none>           <none>
```
