# **Install K8S**
關閉 swap
$ sudo swapoff -a

swap加上註解
$ sudo nano /etc/fstab
#/swap.img       none    swap    sw      0       0

$ sudo nano /etc/sysctl.conf
net.ipv4.ip_forward = 1

$ sudo sysctl -p

$ sudo apt update;sudo apt upgrade -y

安裝 cri-o
$ sudo nano /etc/apt/sources.list.d/devel:kubic:libcontainers:stable.list
deb https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/xUbuntu_20.04/ /

$ sudo nano /etc/apt/sources.list.d/devel:kubic:libcontainers:stable:cri-o:1.20.list
deb http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:/cri-o:/1.20/xUbuntu_20.04/ /

$ curl -L https://download.opensuse.org/repositories/devel:kubic:libcontainers:stable:cri-o:1.20/xUbuntu_20.04/Release.key | sudo  apt-key add -
$ curl -L https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/xUbuntu_20.04/Release.key | sudo  apt-key add -

$ sudo apt-get update
$ sudo apt-get install cri-o cri-o-runc -y

$ sudo sed -i 's/10.85.0.0\/16/10.244.0.0\/16/g' /etc/cni/net.d/100-crio-bridge.conf

$ sudo systemctl daemon-reload
$ sudo systemctl status crio
● crio.service - Container Runtime Interface for OCI (CRI-O)
     Loaded: loaded (/lib/systemd/system/crio.service; disabled; vendor preset: enabled)
     Active: inactive (dead)
       Docs: https://github.com/cri-o/cri-o
       
$ sudo systemctl start crio
$ sudo systemctl enable crio

查看 crio 版本
$ crio version
INFO[0000] Starting CRI-O, version: 1.20.2, git: d5a999ad0a35d895ded554e1e18c142075501a98(clean) 
Version:       1.20.2
GitCommit:     d5a999ad0a35d895ded554e1e18c142075501a98
GitTreeState:  clean
BuildDate:     2021-04-30T13:47:16Z
GoVersion:     go1.15.2
Compiler:      gc
Platform:      linux/amd64
Linkmode:      dynamic

$ sudo reboot

檢查 crio 服務是否自動啟動
$ crio version

------
[K8S]

安裝k8s
$ sudo nano /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main

$ curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

$ sudo apt update

$ sudo apt-cache madison kubeadm | grep 1.20.2
   kubeadm |  1.20.2-00 | https://apt.kubernetes.io kubernetes-xenial/main amd64 Packages

$ export K_VER=1.20.2-00
$ sudo apt install -y kubelet=${K_VER} kubectl=${K_VER} kubeadm=${K_VER}
$ sudo apt-mark hold kubelet kubeadm kubectl

$ sudo nano /etc/default/kubelet
KUBELET_EXTRA_ARGS=--feature-gates="AllAlpha=false,RunAsGroup=true" --container-runtime=remote --cgroup-driver=systemd --container-runtime-endpoint='unix:///var/run/crio/crio.sock' --runtime-request-timeout=5m

$ sudo systemctl daemon-reload
$ sudo systemctl enable kubelet

$ sudo kubeadm config images pull --kubernetes-version=v1.20.2

[config/images] Pulled k8s.gcr.io/kube-apiserver:v1.20.2
[config/images] Pulled k8s.gcr.io/kube-controller-manager:v1.20.2
[config/images] Pulled k8s.gcr.io/kube-scheduler:v1.20.2
[config/images] Pulled k8s.gcr.io/kube-proxy:v1.20.2
[config/images] Pulled k8s.gcr.io/pause:3.2
[config/images] Pulled k8s.gcr.io/etcd:3.4.13-0
[config/images] Pulled k8s.gcr.io/coredns:1.7.0

$ sudo nano /etc/modules
br_netfilter

$ sudo reboot

-----------------------------------------------------------------------------
K8s master

$ sudo kubeadm init --pod-network-cidr=10.244.0.0/16 --cri-socket=/var/run/crio/crio.sock

[master 吐出來的訊息，給worker加入的token]
kubeadm join 120.96.143.167:6443 --token 0jnzay.9rcuhvwsurqzj1t8 --discovery-token-ca-cert-hash sha256:1046ec0feb81bb0b32177e3f1317896b6f93c15cb7a6eaf0654c85435810cd82

-----------------------------------------------------------------------------

[copy cluster token to home]
$ mkdir -p $HOME/.kube
$ sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
$ sudo chown \$(id -u):$(id -g) $HOME/.kube/config

setup kube-router
$ wget https://raw.githubusercontent.com/cloudnativelabs/kube-router/master/daemonset/kubeadm-kuberouter-all-features.yaml

#$ sed -i 's/rbac.authorization.k8s.io\/v1beta1/rbac.authorization.k8s.io\/v1/g' kubeadm-kuberouter-all-features.yaml

$ kubectl create -f kubeadm-kuberouter-all-features.yaml

$ kubectl get pod -n kube-system

$ kubectl taint node 120-96-143-167-m1 node-role.kubernetes.io/master:NoSchedule-

$ kubectl run a1 --image=dafu/alpine.derby --port=8888

$ kubectl get pods -o wide

$ curl http://10.244.0.3:8888
\<h1>Welcome to Spring Boot\</h1>

-----------------------------------------------------------------------------
[join master]

#於 master 執行
$ echo " sudo \`kubeadm token create --print-join-command 2>/dev/null`"
kubeadm join 120.96.143.167:6443 --token 0jnzay.9rcuhvwsurqzj1t8 --discovery-token-ca-cert-hash sha256:1046ec0feb81bb0b32177e3f1317896b6f93c15cb7a6eaf0654c85435810cd82

#於 worker 執行 
$ sudo kubeadm join 120.96.143.167:6443 --token 0jnzay.9rcuhvwsurqzj1t8 --discovery-token-ca-cert-hash sha256:1046ec0feb81bb0b32177e3f1317896b6f93c15cb7a6eaf0654c85435810cd82

#於 master 執行
$ kubectl label node 120-96-143-169-w2 node-role.kubernetes.io/worker=worker
node/120-96-143-169-w2 labeled

-----------------------------------------------------------------------------
#於 master 檢查
$ kubectl get nodes
NAME                        STATUS  ROLES                        AGE   VERSION
120-96-143-167-m1   Ready     control-plane,master     20h   v1.20.2
120-96-143-168-w1   Ready     <none>                          19h   v1.20.2
120-96-143-169-w2   Ready     <none>                          18h   v1.20.2

$ kubectl get pods -o wide
NAME  READY  STATUS  RESTARTS  AGE  IP             NODE                      NOMINATED NODE  READINESS GATES
a1         1/1         running    0                   13s    <none>   120-96-143-167-m1  <none>                     <none>

$ kubectl delete pods a1

$ kubectl run a1 --image=dafu/alpine.derby --port=8888

$ kubectl get pods -o wide
NAME  READY  STATUS                  RESTARTS   AGE   IP             NODE                      NOMINATED NODE  READINESS GATES
a1        0/1         ContainerCreating   0                    13s     <none>   120-96-143-168-w1   <none>           <none>
