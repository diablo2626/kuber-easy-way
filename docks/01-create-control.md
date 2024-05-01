1. Create the configuration file for containerd:
~~~
cat <<EOF | sudo tee /etc/modules-load.d/containerd.conf
overlay
br_netfilter
EOF
~~~
2. Load the modules:
~~~
sudo modprobe overlay
~~~
~~~
sudo modprobe br_netfilter
~~~
3. Set the system configurations for Kubernetes networking:
~~~
cat <<EOF | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF
~~~
4. Apply the new settings:
~~~
sudo sysctl --system
~~~
5. Install containerd:
~~~
sudo apt update
sudo apt install apt-transport-https ca-certificates curl software-properties-common

~~~
~~~
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
~~~
~~~
sudo apt-get update && sudo apt-get install -y containerd.io
~~~
6. Create the default configuration file for containerd:
~~~
sudo mkdir -p /etc/containerd
~~~
7. Generate the default containerd configuration, and save it to the newly created default file:
~~~
sudo containerd config default | sudo tee /etc/containerd/config.toml
~~~
8. Restart containerd to ensure the new configuration file is used:
~~~
sudo systemctl restart containerd
~~~
9. Verify that containerd is running:
~~~
sudo systemctl status containerd
~~~
10. Disable swap:
~~~
sudo swapoff -a
~~~
11. Install the dependency packages:
~~~
sudo apt-get update && sudo apt-get install -y apt-transport-https curl
~~~
12. Download and add the GPG key:
~~~
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30.0/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
~~~
13. Add Kubernetes to the repository list:
~~~
cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30.0/deb/ /
EOF
~~~
14. Update the package listings:
~~~
sudo apt-get update
~~~
15. Install Kubernetes packages:
~~~
sudo apt-get install -y kubelet kubeadm kubectl
~~~
16. Turn off automatic updates:
~~~
sudo apt-mark hold kubelet kubeadm kubectl
~~~
# Initialize the Cluster
1. Initialize the Kubernetes cluster on the control plane node using kubeadm:
~~~
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/calico.yaml
~~~
2. Check the status of the control plane node:
~~~
kubectl get nodes
~~~
# Join the Worker Nodes to the Cluster
~~~
kubeadm token create --print-join-command
~~~
