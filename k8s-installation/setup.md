# Kubernetes cluster setup using kubeadm with vm

## Control Plane Node(s):

- At least 1 VM, preferably 3 or more for high availability.(min 2vcpu, 2gb)
- Components running on control plane nodes include etcd (for cluster state management), API server, scheduler, and controller manager.

- Run the below commands in Master node as a root user

```python
curl -fsSL https://get.docker.com -o install-docker.sh
sudo sh install-docker.sh
sudo chmod 777 /var/run/docker.sock
sudo snap install go --classic
git clone https://github.com/Mirantis/cri-dockerd.git
cd cri-dockerd
mkdir bin
go build -o bin/cri-dockerd
mkdir -p /usr/local/bin
install -o root -g root -m 0755 bin/cri-dockerd /usr/local/bin/cri-dockerd
cp -a packaging/systemd/* /etc/systemd/system
sed -i -e 's,/usr/bin/cri-dockerd,/usr/local/bin/cri-dockerd,' /etc/systemd/system/cri-docker.service
systemctl daemon-reload
systemctl enable cri-docker.service
systemctl enable --now cri-docker.socket
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
kubeadm init --pod-network-cidr "10.244.0.0/16" --cri-socket "unix:///var/run/cri-dockerd.sock"
export KUBECONFIG=/etc/kubernetes/admin.conf
kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml

```

## Worker Node(s):

- At least 1 VM, but typically multiple for scalability and fault tolerance.(min 2vcpu, 2gb)
- Components running on worker nodes include kubelet (for node management), kube-proxy, and container runtime (like Docker or containerd).

- Run the below commands in Worker node as a root user

```python
curl -fsSL https://get.docker.com -o install-docker.sh
sudo sh install-docker.sh
sudo chmod 777 /var/run/docker.sock
sudo snap install go --classic
git clone https://github.com/Mirantis/cri-dockerd.git
cd cri-dockerd
mkdir bin
go build -o bin/cri-dockerd
mkdir -p /usr/local/bin
install -o root -g root -m 0755 bin/cri-dockerd /usr/local/bin/cri-dockerd
cp -a packaging/systemd/* /etc/systemd/system
sed -i -e 's,/usr/bin/cri-dockerd,/usr/local/bin/cri-dockerd,' /etc/systemd/system/cri-docker.service
systemctl daemon-reload
systemctl enable cri-docker.service
systemctl enable --now cri-docker.socket
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
```
- Run the below command in the Master to get the join token
  ```python
  kubeadm token create --print-join-command
  ```
  - copy the token and run in the Worker node add this cri-token to the join command 
  ```python
  --cri-socket "unix:///var/run/cri-dockerd.sock"
  ```
  - to check node added to cluster run the below command
  ```python
  kubectl get nodes
  ```
