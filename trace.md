# K8s setup

Big kudos to [LinuxTechi.com](https://www.linuxtechi.com/install-kubernetes-on-ubuntu-22-04/) for their great guide.

## This is a trace of the commands in the exact order that they were run during our proof of concept setup

These first few commands must be run on both the Master as well as the Worker machines for installing all of their base dependencies

These commands will disable the file system swap files if there are any on your machines

```sh
sudo swapoff -a
sudo sed -i '/^[^#]/ s/\(^.*swap.*$\)/#\1/' /etc/fstab
```

Config

```sh
sudo tee /etc/modules-load.d/containerd.conf <<EOF
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter

sudo tee /etc/sysctl.d/kubernetes.conf <<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF

sudo sysctl --system
```

Repo setup and containerd.io packages

```sh
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmour -o /etc/apt/trusted.gpg.d/docker.gpg

sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

sudo apt update
sudo apt install -y curl gnupg2 software-properties-common apt-transport-https ca-certificates
sudo apt install -y containerd.io
```

Containerd config

```sh
containerd config default | sudo tee /etc/containerd/config.toml >/dev/null 2>&1

sudo sed -i 's/SystemdCgroup \= false/SystemdCgroup \= true/g' /etc/containerd/config.toml

sudo systemctl restart containerd
sudo systemctl enable containerd
```

Kube* installation and config

```sh
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
sudo apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"

sudo apt update
sudo apt install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

## This is where the installations differ

### Commands run on Master VM

The hostname here will depened on your specific setup, use the `hostname` command to get the correct value.

```sh
sudo kubeadm init --control-plane-endpoint=$(hostname)
```

Once the master is initialized, go ahead and follow the on screen commands. These will make your config non-root, and allow you to run commands like `kubectl` as a normal user.

```sh
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

### Commands run on Worker VM

At this point the Master should spit out a join string of some sort, go ahead and copy that command into your worker.

```sh
kubeadm join k8s-master:6443 --token ai45wx.hoo4va2l9arozaxu \
        --discovery-token-ca-cert-hash sha256:3c979ddd77ccf4e12cbbe7ec46585786a47477dc5ea614e8f3a47cce51d7f569
```

In our case, since we setup the control-plane/master using it's hostname, we needed to configure the worker with it's IP address.
Add a line similar to the following to your /etc/hosts if you have the same issue

```sh
10.1.1.21 k8s-master
```
