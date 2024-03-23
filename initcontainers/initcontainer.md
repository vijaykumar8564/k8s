* Init containers:
- init containers are a Kubernetes feature that allows you to run one or more containers before the main application container starts in a Pod.
- Init containers are typically used for pre-initialization tasks, such as fetching configuration files, setting up databases, or performing other setup operations required by the main application container.

* Here are some common use cases for init containers:

* Fetching Configuration Files:
- Init containers can be used to fetch configuration files from external sources or ConfigMaps and store them in a shared volume, making them available to the main application container.

* Initializing Databases:
- Init containers can perform tasks such as initializing databases or setting up database schemas before the main application container starts.

* Installing Dependencies:
- Init containers can install necessary dependencies or packages required by the main application container.

* Waiting for External Resources:
- Init containers can wait for external resources, such as databases or services, to become available before the main application container starts.

* Performing Health Checks:
- Init containers can perform health checks or readiness checks on the environment or external dependencies before the main application container starts.

* Preparing Environment:
- Init containers can set up environment variables, mount volumes, or perform other environment setup tasks required by the main application container.

* In this examle i've used busybox as init container and nginx as main container
- busy box container changed the content of the index.html file
- it will print "New content"
- to access the pod
```python
curl http://10.244.1.238:80
curl http://<pod-ip>:<port>
```

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
      initContainers:
      - name: init-container
        image: busybox
        command: ['sh', '-c', 'echo "New content" > /usr/share/nginx/html/index.html']
        volumeMounts:
        - name: nginx-html
          mountPath: /usr/share/nginx/html
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
        volumeMounts:
        - name: nginx-html
          mountPath: /usr/share/nginx/html
      volumes:
      - name: nginx-html
        emptyDir: {}
```
