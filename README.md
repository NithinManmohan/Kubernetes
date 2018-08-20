# Kubernetes setup, along with YAML files for sample deployment and ingress setup

Repository comprising of sample yaml files for deploying and exposing apps

On a RHEL 7.3 64bit

swapoff –a
echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables
export http_proxy=http://<PROXY_HOST>:<PROXY_PORT>
export https_proxy=http://<PROXY_HOST>:<PROXY_PORT>
export ftp_proxy=http://<PROXY_HOST>:<PROXY_PORT>

[root@vfecare-3 yum.repos.d]# docker --version
Docker version 17.03.0-ce, build 3a232c8

Place docker.socket, docker.service and docker.service.d from attached zip to /etc/system/system/

systemctl daemon-reload

Create kubernetes.repo with the below entries
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg
       https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg

yum clean all
yum clean expire-cache
yum install kubelet kubectl kubeadm

[root@vfecare-3 yum.repos.d]# kubectl version
Client Version: version.Info{Major:"1", Minor:"10", GitVersion:"v1.10.0", GitCommit:"fc32d2f3698e36b93322a3465f63a14e9f0eaead", GitTreeState:"clean", BuildDate:"2018-03-26T16:55:54Z", GoVersion:"go1.9.3", Compiler:"gc", Platform:"linux/amd64"}
Server Version: version.Info{Major:"1", Minor:"10", GitVersion:"v1.10.1", GitCommit:"d4ab47518836c750f9949b9e0d387f20fb92260b", GitTreeState:"clean", BuildDate:"2018-04-12T14:14:26Z", GoVersion:"go1.9.3", Compiler:"gc", Platform:"linux/amd64"}
   
Edit /etc/systemd/system/kubelet.service.d/10-kubeadm.conf and replace ExecStart parameter with the below one
ExecStart=/usr/bin/kubelet $KUBELET_KUBECONFIG_ARGS $KUBELET_SYSTEM_PODS_ARGS $KUBELET_DNS_ARGS $KUBELET_AUTHZ_ARGS $KUBELET_CADVISOR_ARGS $KUBELET_CGROUP_ARGS $KUBELET_CERTIFICATE_ARGS $KUBELET_EXTRA_ARGS

kubeadm init --apiserver-advertise-address=<IP_ADDRESS>

mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

kubectl label nodes <HOSTNAME> dedicated=master
kubectl taint nodes --all node-role.kubernetes.io/master-
export KUBECONFIG=/etc/kubernetes/admin.conf

kubectl create ns <CUSTOM_NS>

kubectl create -f <DEPLOYMENT_YAML> --namespace=<CUSTOM_NS>
kubectl create -f <SERVICE_YAML> --namespace=<CUSTOM_NS>
