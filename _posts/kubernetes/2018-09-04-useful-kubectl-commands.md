### pod

```
kubectl -n testns get pod -o wide
kubectl -n testns get pod testpod -o yaml
kubectl -n testns describe pod testpod
kubectl -n testns logs --tail=50 testpod 
kubectl -n testns delete pod testpod --force=true --period=0
```

### config

```
kubectl config current-context
kubectl config set-context $(kubectl config current-context) --namespace="mydefault"
kubectl config view
kubectl config set-context $(kubectl config current-context) --namespace=""
```

### cronjob

```
kubectl get jobs --all-namespaces
kubectl  -n testns get quota -o yaml

kubectl get cronjob --all-namespaces
kubectl  -n testns get cronjob
kubectl  -n testns get jobs

```

### controllermanager

```
kubectl -n testns get cm testcm -o yaml
```

### node
```
kubectl get nodes
kubectl describe nodetest
kubectl get nodes --show-labels


```
