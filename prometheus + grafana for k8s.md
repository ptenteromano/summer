# Deploy prometheus + grafana for k8s

## Prerequisite
We need a ready and all running k8s cluster (referring to Deploy_k8s_cluster.md)

1. On master 
```
git clone https://github.com/redhatxl/k8s-prometheus-grafana.git
```

2. On master and slaves
```
docker pull prom/node-exporter
docker pull prom/prometheus:v2.0.0
docker pull grafana/grafana:4.2.0
```

3. On master
```
kubectl create -f  k8s-prometheus-grafana/node-exporter.yaml 
kubectl create -f  k8s-prometheus-grafana/prometheus/rbac-setup.yaml
kubectl create -f  k8s-prometheus-grafana/prometheus/configmap.yaml 
kubectl create -f  k8s-prometheus-grafana/prometheus/prometheus.deploy.yml 
kubectl create -f  k8s-prometheus-grafana/prometheus/prometheus.svc.yml 
kubectl create -f   k8s-prometheus-grafana/grafana/grafana-deploy.yaml
kubectl create -f   k8s-prometheus-grafana/grafana/grafana-svc.yaml
kubectl create -f   k8s-prometheus-grafana/grafana/grafana-ing.yaml
```

4. Check IP and ports
```
dig +short myip.opendns.com @resolver1.opendns.com
128.110.153.147

kubectl get svc -n kube-system
NAME            TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                  AGE
grafana         NodePort    10.104.149.229   <none>        3000:30435/TCP           58s
kube-dns        ClusterIP   10.96.0.10       <none>        53/UDP,53/TCP,9153/TCP   2m7s
node-exporter   NodePort    10.103.17.118    <none>        9100:31672/TCP           60s
prometheus      NodePort    10.104.249.31    <none>        9090:30003/TCP           59s
```

5. Use 128.110.153.147:30435 to access the web interface
```
User: admin
Password: admin
```



#reference
https://blog.51cto.com/kaliarch/2160569
