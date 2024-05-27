Create K8s Cluster and Configure Master and NodeS
install kubeadm install k8s
```
# Install Docker CE on all servers
## Set up the repository:
### Install packages to allow apt to use a repository over HTTPS
apt-get update && apt-get install -y \
  apt-transport-https ca-certificates curl software-properties-common


### Add Docker’s official GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -

### Add Docker apt repository.
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmour -o /etc/apt/trusted.gpg.d/docker.gpg
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
echo 1 > /proc/sys/net/ipv4/ip_forward
sudo apt update
sudo apt install -y containerd.io

containerd config default | sudo tee /etc/containerd/config.toml >/dev/null 2>&1
sudo sed -i 's/SystemdCgroup \= false/SystemdCgroup \= true/g' /etc/containerd/config.toml

sudo systemctl restart containerd
sudo systemctl enable containerd


Installing kubeadm, kubelet and kubectl on all servers

sudo apt-get update && sudo apt-get install -y apt-transport-https curl
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl



Configure Master and NodeS
kubeadm init --pod-network-cidr=10.244.0.0/16 ( on master as root user)

On master:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

On Nodes:

kubeadm join 172.31.53.56:6443 --token cbjmz6.bpgu7hn3t74l6b3s \
    --discovery-token-ca-cert-hash sha256:cd0d4c56a9a81f287adf4cf221998a83a082ad768610693de100595ca1fe457


on Master:

kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml


kubectl get nodes
kubectl get pods --all-namespaces
kubectl get pods
kubectl describe pod/hello-pod
kubectl delete pod/hello-pod

```
