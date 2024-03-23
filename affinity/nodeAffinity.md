* affinity
- "affinity" refers to the mechanism used to influence the scheduling of pods onto nodes in the cluster based on certain rules or constraints.

* nodeAffinity:
- Node affinity allows you to specify rules that influence the scheduling of pods based on the characteristics of individual nodes.
- For example, you can specify that a pod should be scheduled onto nodes with certain labels or with a certain amount of available resources.

* preferredDuringSchedulingIgnoredDuringExecution:
- preferredDuringSchedulingIgnoredDuringExecution indicates that the rule is a soft preference and Kubernetes should try to satisfy it if possible but can ignore it during execution if necessary.


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
      affinity:
        nodeAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
           - weight: 100
             preference: 
              matchExpressions:
                - key: env
                  operator: In
                  values: 
                  - "dev"
      containers:
      - name: my-container
        image: nginx
        ports:
        - containerPort: 80
```
* pods created in both nodes

```python
kubectl get po -o wide
NAME                      READY   STATUS    RESTARTS   AGE   IP             NODE     NOMINATED NODE   READINESS GATES
my-app-54dc7fc456-4plwv   1/1     Running   0          15s   10.244.0.45    master   <none>           <none>
my-app-54dc7fc456-5v7sj   1/1     Running   0          15s   10.244.1.152   node     <none>           <none>
my-app-54dc7fc456-66v9l   1/1     Running   0          15s   10.244.1.156   node     <none>           <none>
my-app-54dc7fc456-ddcxf   1/1     Running   0          15s   10.244.1.155   node     <none>           <none>
my-app-54dc7fc456-lf2td   1/1     Running   0          15s   10.244.0.46    master   <none>           <none>
my-app-54dc7fc456-pkdp7   1/1     Running   0          15s   10.244.0.48    master   <none>           <none>
my-app-54dc7fc456-qk4vd   1/1     Running   0          15s   10.244.0.47    master   <none>           <none>
my-app-54dc7fc456-vshz6   1/1     Running   0          15s   10.244.1.153   node     <none>           <none>
my-app-54dc7fc456-w6pxb   1/1     Running   0          15s   10.244.1.151   node     <none>           <none>
my-app-54dc7fc456-wxhjw   1/1     Running   0          15s   10.244.1.154   node     <none>           <none>
```




* requiredDuringSchedulingIgnoredDuringExecution:
- 

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
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
           nodeSelectorTerms:
           - matchExpressions:
             - key: env
               operator: In
               values:
               - "dev"
      containers:
      - name: my-container
        image: nginx
        ports:
        - containerPort: 80
```

* if there is no node labeled as mentioned in the deployment file it will not deeploy into other nodes.

```python
kubectl get po -o wide
NAME                      READY   STATUS    RESTARTS   AGE   IP       NODE     NOMINATED NODE   READINESS GATES
my-app-5dc75f9877-29prt   0/1     Pending   0          9s    <none>   <none>   <none>           <none>
my-app-5dc75f9877-4jqrs   0/1     Pending   0          9s    <none>   <none>   <none>           <none>
my-app-5dc75f9877-7567n   0/1     Pending   0          9s    <none>   <none>   <none>           <none>
my-app-5dc75f9877-8c62q   0/1     Pending   0          9s    <none>   <none>   <none>           <none>
my-app-5dc75f9877-ccsd9   0/1     Pending   0          9s    <none>   <none>   <none>           <none>
my-app-5dc75f9877-s2dnm   0/1     Pending   0          9s    <none>   <none>   <none>           <none>
my-app-5dc75f9877-v2fpq   0/1     Pending   0          9s    <none>   <none>   <none>           <none>
my-app-5dc75f9877-v7k2g   0/1     Pending   0          9s    <none>   <none>   <none>           <none>
my-app-5dc75f9877-wtxhx   0/1     Pending   0          9s    <none>   <none>   <none>           <none>
my-app-5dc75f9877-x8bg9   0/1     Pending   0          9s    <none>   <none>   <none>           <none>
```
- Now label a node with given value

```python
kubectl label node node env=dev
```
- then only pods created in specific node only

```python
kubectl get po -o wide
NAME                      READY   STATUS    RESTARTS   AGE     IP             NODE   NOMINATED NODE   READINESS GATES
my-app-5dc75f9877-29prt   1/1     Running   0          5m58s   10.244.1.165   node   <none>           <none>
my-app-5dc75f9877-4jqrs   1/1     Running   0          5m58s   10.244.1.166   node   <none>           <none>
my-app-5dc75f9877-7567n   1/1     Running   0          5m58s   10.244.1.160   node   <none>           <none>
my-app-5dc75f9877-8c62q   1/1     Running   0          5m58s   10.244.1.163   node   <none>           <none>
my-app-5dc75f9877-ccsd9   1/1     Running   0          5m58s   10.244.1.161   node   <none>           <none>
my-app-5dc75f9877-s2dnm   1/1     Running   0          5m58s   10.244.1.159   node   <none>           <none>
my-app-5dc75f9877-v2fpq   1/1     Running   0          5m58s   10.244.1.162   node   <none>           <none>
my-app-5dc75f9877-v7k2g   1/1     Running   0          5m58s   10.244.1.164   node   <none>           <none>
my-app-5dc75f9877-wtxhx   1/1     Running   0          5m58s   10.244.1.158   node   <none>           <none>
my-app-5dc75f9877-x8bg9   1/1     Running   0          5m58s   10.244.1.157   node   <none>           <none>

```

