### Objects and instances
```
$ kubectl delete deployments/my-nginx services/my-nginx-svc

$ kubectl delete deployment,services -l app=nginx
deployment.apps "my-nginx" deleted
service "my-nginx-svc" deleted

```

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

### rolling update a deployment 

- Note: 
> A Deployment’s rollout is triggered if and only if the Deployment’s pod template (that is, .spec.template) is changed, for example if the labels or container images of the template are updated. Other updates, such as scaling the Deployment, do not trigger a rollout.

``` shell 
kubectl set image deploy frontend php-redis=gcr.io/google-samples/gb-frontend:v3 --record

kubectl get rs -w
kubectl rollout pause deploy/frontend
kubectl rollout resume deployment/review-demo --namespace=scm
kubectl rollout undo deploy frontend --to-revision=7

// deployment/depName
kubectl rollout status deployment/review-demo  --namespace=scm
kubectl describe deployment/review-demo  --namespace=scm
// deployment depName
kubectl rollout status deployments review-demo --namespace=scm
kubectl describe deployments review-demo  --namespace=scm

```
