# Prerequisite
Running k8s cluster, for detail please visit [repo readme](https://github.com/ptenteromano/summer/blob/master/README.md)

Using the rnn-save-load as an example

# Deploy PersistentVolume and PersistentVolumeClaim
```
kubectl apply -f pv.yaml
kubectl apply -f pvc.yaml
```
# Run the deep learning job
```
kubectl apply -f rnn-test.yaml
```
# Save and load
the output logs and DL-models are saved under dir `/mnt/data`
to load the model data and continue training, simply deploy the DL job again
```
kubectl delete pods rnn-test
kubectl apply -f rnn-test.yaml
```
