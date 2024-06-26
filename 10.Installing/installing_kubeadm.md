# Solution – Install a Kubernetes Cluster using kubeadm

## 1. Install the ``kubeadm ``and ``kubelet`` packages on the ``controlplane`` and ``node01``.

Use the exact version of ``1.26.0-00`` for both.

- kubeadm installed on controlplane ?
- kubelet installed on controlplane?
- Kubeadm installed on worker node01 ?
- Kubelet installed on worker node01 ?

**Hint**: 
Refer to the official k8s documentation - https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/

and follow the installation steps.

Solution: 
These steps have to be performed on both nodes.

``set net.bridge.bridge-nf-call-iptables to 1:``
```bash
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
br_netfilter
EOF

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sudo sysctl --system
```
The container runtime has already been installed on both nodes, so you may skip this step.
Install ``kubeadm, kubectl and kubelet`` on all nodes:
```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl

sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg

echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt-get update
sudo apt-get install -y kubelet=1.26.0-00 kubeadm=1.26.0-00 kubectl=1.26.0-00
sudo apt-mark hold kubelet kubeadm kubectl
```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash
```

