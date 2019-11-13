# Deploy k8s cluster

#### First install docker and k8s on both master and slaves nodes:
1. First run the following commands:
   - `sudo -s`
   - `cd /`
2. On Master node run `./k8s.sh`
3. On Worker nodes run `./k8s_worker.sh`
4. On Master, copy output that looks like this example (from `kubeadm init`):
   - Note: to see output, you can run `kubeadm token create --print-join-command`

```
kubeadm join 128.110.153.78:6443 --token 7cmbqs.zx7j57hzp64zwcei \
    --discovery-token-ca-cert-hash sha256:ac04125e58767ddf33e390848a7b9beac0e79b537393998b0349296e72cc6bc6
```
5. On every Worker node copy and run what you got from above.
6. Check status of nodes from Master (two seperate commands):
```
kubectl get nodes
kubectl get pod --all-namespaces
```
7. All nodes should be ready: (example)
```
NAME                                               STATUS   ROLES    AGE     VERSION
cp-1.zsl3203-qv50305.shield-pg0.wisc.cloudlab.us   Ready    <none>   99s     v1.14.1
cp-2.zsl3203-qv50305.shield-pg0.wisc.cloudlab.us   Ready    <none>   97s     v1.14.1
ctl.zsl3203-qv50305.shield-pg0.wisc.cloudlab.us    Ready    master   3m49s   v1.14.1
```
