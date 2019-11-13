# Prerequisite
Running k8s cluster, for detail please visit [repo readme](https://github.com/ptenteromano/summer/blob/master/README.md)

Using the rnn-save-load as an example

# Deploy PersistentVolume and PersistentVolumeClaim
- cd into directory `cd /summer/rnn-save-load`
```
kubectl apply -f pv.yaml
kubectl apply -f pvc.yaml
```
# Run the deep learning job
```
kubectl apply -f rnn-test.yaml
```
# Check on running pod
```
kubectl get pods
```
# Save and load
The output logs and DL-models are saved under dir `/mnt/data`

`cd /mnt/data && ls`

To restart training, delete pod and deploy the DL job again
```
kubectl delete pods rnn-test
kubectl apply -f rnn-test.yaml
```
