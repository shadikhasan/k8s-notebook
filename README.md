# 🚀 Kubernetes Cluster Setup using Kubeadm + Calico

This guide explains how to set up a Kubernetes cluster using `kubeadm` and `Calico` as the CNI plugin.

---

## ✅ 0. Configure Static Network (All Nodes)

Ensure your network settings are stable (especially for multi-node clusters). Using Netplan:

### Edit Netplan Configuration

```bash
sudo nano /etc/netplan/00-installer-config.yaml
```

Example static IP configuration:

```yaml
network:
  version: 2
  ethernets:
    eth0:
      dhcp4: no
      addresses:
        - 192.168.1.100/24
      gateway4: 192.168.1.1
      nameservers:
        addresses:
          - 8.8.8.8
          - 8.8.4.4
```

> Replace `eth0` with your actual interface name (`ip a` to check) and adjust the IP settings accordingly.

### Apply the Netplan Configuration

```bash
sudo netplan try
sudo netplan apply
```

---

## ✅ 1. Prepare the System (All Nodes)

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y curl apt-transport-https ca-certificates software-properties-common ufw
```

### Disable swap:

```bash
sudo swapoff -a
sudo sed -i '/ swap / s/^/#/' /etc/fstab
```

### Load required kernel modules:

```bash
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter
```

### Apply sysctl settings:

```bash
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF

sudo sysctl --system
```

---

## ✅ 2. Install containerd (All Nodes)

```bash
sudo apt install -y containerd
```

### Configure containerd:

```bash
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml
```

### Edit config and set cgroup driver to `systemd`:

```bash
sudo nano /etc/containerd/config.toml
```

Find the following section and set:

```toml
[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
  SystemdCgroup = true
```

### Restart containerd:

```bash
sudo systemctl restart containerd
sudo systemctl enable containerd
```

---

## ✅ 3. Install Kubernetes components (All Nodes)

```bash
# Create keyring directory
sudo mkdir -p -m 755 /etc/apt/keyrings

# Add signing key
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.33/deb/Release.key | \
  sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

# Add Kubernetes 1.33 repository
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.33/deb/ /' | \
  sudo tee /etc/apt/sources.list.d/kubernetes.list

# Update and install Kubernetes tools
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

---

## ✅ 4. Open Required Ports (All Nodes)

### Master Node:

```bash
sudo ufw allow 6443/tcp      # API Server
sudo ufw allow 2379:2380/tcp # etcd
sudo ufw allow 10250/tcp     # Kubelet API
sudo ufw allow 10257/tcp     # Controller Manager
sudo ufw allow 10259/tcp     # Scheduler
sudo ufw allow 179/tcp       # Calico BGP
```

### Worker Nodes:

```bash
sudo ufw allow 10250/tcp
sudo ufw allow 30000:32767/tcp  # NodePort services
sudo ufw allow 179/tcp          # Calico BGP
```

---

## ✅ 5. Initialize Kubernetes Cluster (Master Only)

```bash
sudo kubeadm init --pod-network-cidr=192.168.0.0/16
```

### Set up kubeconfig:

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

#### Print the joining token (Optional)

```bash
sudo kubeadm token create --print-join-command
```

---

## ✅ 6. Install Calico Network Plugin (Master Only)

```bash
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.27.0/manifests/calico.yaml
```

### Verify Calico is running:

```bash
kubectl get pods -n kube-system
```

---

## ✅ 7. Join Worker Nodes to the Cluster

On each worker node, run the command printed by `kubeadm init`, which looks like:

```bash
kubeadm join <MASTER_IP>:6443 --token <TOKEN> --discovery-token-ca-cert-hash sha256:<HASH>
```

---

## ✅ 8. Verify Cluster Status (Master Node)

```bash
kubectl get nodes
kubectl get pods -A
```

All nodes should show `Ready` status and pods should be `Running`.

---

## ✅ Done!

You now have a working Kubernetes cluster using kubeadm and Calico! 🎉

# ⭐ Essential k8s command:
### Get nodes in the kube-system namespace
```bash
kubectl get nodes
```
### Get nodes in the kube-system namespace with extra details
```bash
kubectl get nodes -o wide
```
### Get pods in the kube-system namespace
```bash
kubectl get pods -n kube-system
```
### Get all resources (pods, services, deployments, etc.) in the kube-system namespace
```bash
kubectl get all -n kube-system
```