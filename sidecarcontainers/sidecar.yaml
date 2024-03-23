# sidecarcontainer:

* The sidecar pattern is about co-locating another container in a pod in addition to the main application container.
* The application container is unaware of the sidecar container and just goes about its business.
* One Pod can host many containers, using the same of different Docker images.
* Containers in the Pod can communicate using the localhost address
* The sidecar container will use the same network namespace as of the main container so you don't have to assign any network interfaces or ip addresses to the sidecar  container.
* The containers will talk to each other using different ports.
* Each container has it's own filesystem, but volumes are defined at the Pod level and can be mounted into multiple containers to share data
* Let us quickly deploy a single pod with two containers i.e one sidecar container with main container. Here is my YAML file to create this Pod:

```python
apiVersion: v1
kind: Pod
metadata:
  name: sidecar-pod-1
spec:
  volumes:
  - name: log
    emptyDir: {}

  containers:
  - image: busybox
    name: main-container
    args:
     - /bin/sh
     - -c
     - >
      while true; do
        echo "$(date) INFO hello from main-container" >> /var/log/myapp.log ;
        sleep 1;
      done
    volumeMounts:
    - name: log
      mountPath: /var/log

  - name: sidecar-container
    image: busybox
    args:
     - /bin/sh
     - -c
     - tail -fn+1 /var/log/myapp.log
    volumeMounts:
    - name: log
      mountPath: /var/log
```

## Let us understand this YAML file:

* For the sake of understanding I have named my containers as main-container and sidecar-container.
* The main container will be our application which will continuously write something to /var/log/myapp.log
* The /var/log/ path is mounted on the containers using separate volume.
* This path is mounted using volumeMounts in both the containers so that the path is shared across both the containers.
* The sidecar container will read the log file content using tail -fn+1 /var/log/myapp.log

* Let us create this Pod:
```python
kubectl create -f sidecar-example.yaml
```

* List all containers from the pod:

```python
kubectl get pods sidecar-pod-1 -o jsonpath='{.spec.containers[*].name}'
```
* So we have two containers inside sidecar-pod-1:

  - main-container
  - sidecar-container

* Verify communication between main and sidecar container:
```python
kubectl logs -f sidecar-pod-1 -c sidecar-container
```

* Verify Network Namespace between main and sidecar container
```python
kubectl get pods -o wide
```

* Both containers share same network and hostname

