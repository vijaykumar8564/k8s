# Cluster Management Commands:
  * cluster-info    Display cluster information
  * top             Display resource (CPU/memory) usage
  * cordon          Mark node as unschedulable
  ```python
  kubectl cordon <node-name>
  The kubectl cordon command is used in Kubernetes to mark a node as unschedulable. When you "cordon" a node, Kubernetes will prevent new pods from being scheduled onto that node. However, it allows existing pods on the node to continue running until they terminate or are manually moved elsewhere.

  ```
* uncordon        Mark node as schedulable
  ```python
  kubectl uncordon <node-name>
  The kubectl cordon command is used in Kubernetes to mark a node as schedulable.
  ```

* drain           Drain node in preparation for maintenance
  ```python
  kubectl drain node
  ```



  
* Taints:
    - Apply Taints: You can apply a taint to a node using the kubectl taint command. Taints have three components:

    - Key: The name of the taint.
    - Value: The value associated with the taint (optional).
    - Effect: The effect of the taint, which can be one of three values:
        - NoSchedule: Pods are not scheduled onto the node unless they have a corresponding toleration.
        - PreferNoSchedule: Similar to NoSchedule, but Kubernetes will try to avoid placing pods on the node if possible.
        - NoExecute: Existing pods on the node without tolerations are evicted (removed) if the taint is added or updated with this effect.
    - example
    ```python
    kubectl taint nodes <node-name> key=value:effect
    ```
    - to check the taints on node
    ```python
    kubectl describe node <node-name> | grep "Taints"
    ```
    - untaint node
    ```python
    kubectl taint node master node-role.kubernetes.io/control-plane-
    ```

* Tolerations:
    - Define Tolerations in 
    - Pod Spec: To allow a pod to be scheduled onto a tainted node, you need to specify a toleration in the pod's YAML configuration.
    -  A toleration specifies the taint key, value, and effect that the pod tolerates.
    - example:
    ```python
    tolerations:
    - key: "key"
      operator: "Equal"
      value: "value"
      effect: "NoSchedule"
    ```
* Use Cases:
  - Dedicated Nodes: You can taint nodes to dedicate them to specific types of workloads, such as machine learning jobs, databases, or system components.

  - Isolation: Tainting nodes can be used to isolate certain nodes for specific purposes, such as running experimental or high-priority workloads.

  - Resource Allocation: Taints can help manage resource allocation by ensuring that critical resources are reserved for specific types of workloads.

  - Node Maintenance: Tainting a node during maintenance prevents new pods from being scheduled onto the node until the maintenance is complete, ensuring that existing workloads are not disrupted



# scenario1:
  - lets cordon node
  ```python
  kubectl crodon <node-name>
  ```
  - try to create the pod using the below command
  ```python
  kubectl run nginx-pod --image=nginx --port=80
  ```
  - check the pod creation

  ```python
  kubectl get po
  NAME        READY   STATUS    RESTARTS   AGE
  nginx-pod   0/1     Pending   0          6s
  ```
  - pod is in pending state 
  - Lets check the pod events using the descibe command
  ```python
  kubectl describe pod <pnginx-pod>
  ```
  - you can see the events lke this

  ```python
  Events:
  Type     Reason            Age    From               Message
  ----     ------            ----   ----               -------
  Warning  FailedScheduling  3m16s  default-scheduler  
  -0/2 nodes are available:
    1 node(s) had untolerated taint {node-role.kubernetes.io/control-plane: },
    1 node(s) were unschedulable.
    preemption: 0/2 nodes are available: 2 
    Preemption is not helpful for scheduling.

  ```
  - Here you can see Master node by default tainted.
  - Worker node is unschedulable
  - Lets uncordn the node
  ```python
  kubectl uncordon node
  ```
  - Lets check the pod events using the descibe command
  ```python
  kubectl describe pod <pnginx-pod>
  ```
  - You can see like this

  ```python
  Events:
  Type     Reason            Age                   From               Message
  ----     ------            ----                  ----               -------
  Normal   Scheduled         4s                    default-scheduler  Successfully assigned default/nginx-pod to node
  Normal   Pulling           3s                    kubelet            Pulling image "nginx"
  Normal   Pulled            3s                    kubelet            Successfully pulled image "nginx" in 172ms (172ms including waiting)
  Normal   Created           3s                    kubelet            Created container nginx-pod
  Normal   Started           3s                    kubelet            Started container nginx-pod
  ```

# scenarion 2:

- Lets try to drain the node
```python
kubectl drain node
```
- it will throw you error like this 
```python
node/node cordoned
error: unable to drain node "node" due to error:[cannot delete Pods declare no controller (use --force to override): default/nginx-pod, cannot delete DaemonSet-managed Pods (use --ignore-daemonsets to ignore): kube-flannel/kube-flannel-ds-jlm66, kube-system/kube-proxy-kpg95], continuing command...
There are pending nodes to be drained:
 node
cannot delete Pods declare no controller (use --force to override): default/nginx-pod
cannot delete DaemonSet-managed Pods (use --ignore-daemonsets to ignore): kube-flannel/kube-flannel-ds-jlm66, kube-system/kube-proxy-kpg95
```
- when you run the command drain first it is cordoned the node for preventing new pod schedule onto this node.
- daemonsets to be run on the nodes you can use the flag as mentioned below
```python
kubectl drain node --ignore-daemonsets
```
- again you will see error

```python
node/node already cordoned
error: unable to drain node "node" due to error:cannot delete Pods declare no controller (use --force to override): default/nginx-pod, continuing command...
There are pending nodes to be drained:
 node
cannot delete Pods declare no controller (use --force to override): default/nginx-pod
```
-  Now you can use the --force to delete the pods
```python
kubectl drain node --force --ignore-daemonsets
```
- finally succesfully drain the node but pods are delete from the node

```python
node/node already cordoned
Warning: deleting Pods that declare no controller: default/nginx-pod; ignoring DaemonSet-managed Pods: kube-flannel/kube-flannel-ds-jlm66, kube-system/kube-proxy-kpg95
evicting pod default/nginx-pod
pod/nginx-pod evicted
node/node drained
```

# scenario 3:

- taint the node try to deploy the pod 
```python
kubectl taint node env=dev:NoSchedule
```
- write the pod manifest pod.yaml

```python
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: pod-nginx
  name: pod-nginx
spec:
  containers:
  - image: nginx
    name: pod-nginx
    ports:
    - containerPort: 80
```
- create the pod
```python
kubectl create -f pod.yaml
```
- check the pod events with kubectl describe pod-name
- you will find this error
- Lets add Toleartions to the pod
```python
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: pod-nginx
  name: pod-nginx
spec:
  containers:
  - image: nginx
    name: pod-nginx
    ports:
    - containerPort: 80
  tolerations:
    - key: "env"
      operator: "Equal"
      value: "dev"
      effect: "NoSchedule"
```
- Now pod is running in the same node 

```python
kubectl get po -o wide
NAME        READY   STATUS    RESTARTS   AGE     IP            NODE   NOMINATED NODE   READINESS GATES
pod-nginx   1/1     Running   0          9m51s   10.244.1.12   node   <none>           <none>
```

# scenarion 4 
- lets drain node but pods running on the node are to be run withouth interuption 
- to make this possble you need to define the pod disruption budget
- lets untaint the nodes
- create deployment
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
```

- pods will run on both nodes

```python
kubectl get po -o wide
NAME                      READY   STATUS    RESTARTS   AGE   IP            NODE     NOMINATED NODE   READINESS GATES
my-app-69f64668bd-49hw9   1/1     Running   0          14s   10.244.0.15   master   <none>           <none>
my-app-69f64668bd-98mzt   1/1     Running   0          14s   10.244.1.13   node     <none>           <none>
my-app-69f64668bd-9pksz   1/1     Running   0          14s   10.244.1.14   node     <none>           <none>
my-app-69f64668bd-bjxzw   1/1     Running   0          14s   10.244.1.15   node     <none>           <none>
my-app-69f64668bd-btvtq   1/1     Running   0          14s   10.244.0.18   master   <none>           <none>
my-app-69f64668bd-dcfnn   1/1     Running   0          14s   10.244.1.18   node     <none>           <none>
my-app-69f64668bd-pphpc   1/1     Running   0          14s   10.244.1.16   node     <none>           <none>
my-app-69f64668bd-s7s2g   1/1     Running   0          14s   10.244.0.16   master   <none>           <none>
my-app-69f64668bd-vcv49   1/1     Running   0          14s   10.244.0.17   master   <none>           <none>
my-app-69f64668bd-zfscj   1/1     Running   0          14s   10.244.1.17   node     <none>           <none>
```


- Define the pod disruptionbudget

```python
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: frontend-pdb
spec:
  minAvailable: 870%
  selector:
    matchLabels:
      app: my-app
```

- pod disruption budget 

```python
kubectl get poddisruptionbudget.policy
NAME           MIN AVAILABLE   MAX UNAVAILABLE   ALLOWED DISRUPTIONS   AGE
frontend-pdb   70%             N/A               3                     5s
```

- now drain the node wiith --ignore-daemonsets

```python
kubectl drain node --ignore-daemonsets
```
- Now all pods are placed in master node
```python
kubectl get po -o wide
NAME                      READY   STATUS    RESTARTS   AGE    IP            NODE     NOMINATED NODE   READINESS GATES
my-app-69f64668bd-2v8fw   1/1     Running   0          9s     10.244.0.22   master   <none>           <none>
my-app-69f64668bd-49hw9   1/1     Running   0          7m1s   10.244.0.15   master   <none>           <none>
my-app-69f64668bd-5xkbs   1/1     Running   0          9s     10.244.0.24   master   <none>           <none>
my-app-69f64668bd-btvtq   1/1     Running   0          7m1s   10.244.0.18   master   <none>           <none>
my-app-69f64668bd-hblkh   1/1     Running   0          14s    10.244.0.20   master   <none>           <none>
my-app-69f64668bd-hqrx4   1/1     Running   0          14s    10.244.0.19   master   <none>           <none>
my-app-69f64668bd-s7s2g   1/1     Running   0          7m1s   10.244.0.16   master   <none>           <none>
my-app-69f64668bd-twlq8   1/1     Running   0          14s    10.244.0.21   master   <none>           <none>
my-app-69f64668bd-vcv49   1/1     Running   0          7m1s   10.244.0.17   master   <none>           <none>
my-app-69f64668bd-whfm5   1/1     Running   0          9s     10.244.0.23   master   <none>           <none>
```
