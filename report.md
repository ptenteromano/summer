# Report for passing user defined policy to kube-scheduler
## Envirnoment : Ubuntu 16.04

1. Below is the code for deploying k8s cluster
```
apt-get update -y
swapoff -a

apt-get update && apt-get install apt-transport-https ca-certificates curl software-properties-common

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
add-apt-repository \
  "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) \
  stable"

apt-get update && apt-get install docker-ce=18.06.2~ce~3-0~ubuntu

cat > /etc/docker/daemon.json <<EOF
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF

mkdir -p /etc/systemd/system/docker.service.d

systemctl daemon-reload
systemctl restart docker

apt-get update && apt-get install -y apt-transport-https curl

curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -

cat << EOF >/etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF

apt-get update
apt-get install -y kubelet kubeadm kubectl
apt-mark hold kubelet kubeadm kubectl
```

2. Then I try to pass the conf to scheduler throught kubeadm init
```
kubeadm init --conf scheduler.yaml
```

3. Where scheduler.yaml is as following
```
apiVersion: kubeadm.k8s.io/v1beta2
kind: ClusterConfiguration
kubernetesVersion: v1.15.0
scheduler:
  extraArgs:
    policy-config-file: /summer/conf.json
```

4. And the conf.json is an example scheduler policy conf file provided by k8s
```
{
"kind" : "Policy",
"apiVersion" : "v1",
"predicates" : [
	{"name" : "PodFitsPorts"},
	{"name" : "PodFitsResources"},
	{"name" : "NoDiskConflict"},
	{"name" : "MatchNodeSelector"},
	{"name" : "HostName"}
	],
"priorities" : [
	{"name" : "LeastRequestedPriority", "weight" : 1},
	{"name" : "BalancedResourceAllocation", "weight" : 1},
	{"name" : "ServiceSpreadingPriority", "weight" : 1},
	{"name" : "EqualPriority", "weight" : 1}
	]
}
```

5. Some additional step for deploying k8s
```
mkdir -p $HOME/.kube
cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
chown $(id -u):$(id -g) $HOME/.kube/config

kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"

kubectl taint nodes --all node-role.kubernetes.io/master-

kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

6. However, the deployment failed and the scheduler keeps crashing
```
kubectl get nodes
NAME                                      STATUS     ROLES    AGE    VERSION
master.test.shield-pg0.utah.cloudlab.us   NotReady   master   154m   v1.15.0

kubectl get pods --all-namespaces
NAMESPACE     NAME                                                              READY   STATUS             RESTARTS   AGE
kube-system   coredns-5c98db65d4-6zvs8                                          0/1     Pending            0          155m
kube-system   coredns-5c98db65d4-9n8qc                                          0/1     Pending            0          155m
kube-system   etcd-master.test.shield-pg0.utah.cloudlab.us                      1/1     Running            0          154m
kube-system   kube-apiserver-master.test.shield-pg0.utah.cloudlab.us            1/1     Running            0          154m
kube-system   kube-controller-manager-master.test.shield-pg0.utah.cloudlab.us   1/1     Running            0          154m
kube-system   kube-proxy-rcdf2                                                  0/1     Pending            0          155m
kube-system   kube-scheduler-master.test.shield-pg0.utah.cloudlab.us            0/1     CrashLoopBackOff   35         155m
kube-system   weave-net-w9949                                                   0/2     Pending            0          155m
```

