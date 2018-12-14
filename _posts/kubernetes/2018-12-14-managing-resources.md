### Organizing resource configurations

[Management](https://kubernetes.io/docs/concepts/cluster-administration/manage-deployment/) of multiple resources can be simplified by grouping them together in the same file (separated by --- in YAML). 

Therefore, **it’s best to specify the service first**, since that will ensure the scheduler can spread the pods associated with the service as they are created by the controller(s), such as Deployment.

### Bulk operations in kubectl
``` shell
$ kubectl delete -f https://k8s.io/examples/application/nginx-app.yaml
deployment.apps "my-nginx" deleted
service "my-nginx-svc" deleted

$ kubectl delete deployments/my-nginx services/my-nginx-svc

$ kubectl delete deployment,services -l app=nginx
deployment.apps "my-nginx" deleted
service "my-nginx-svc" deleted


//performing a bulk operation on project/k8s/development recursively 
$ kubectl create -f project/k8s/development --recursive
configmap/my-config created
deployment.apps/my-deployment created
persistentvolumeclaim/my-pvc created
```

### Using labels effectively
``` shell
/*frontend: 
     labels:
        app: guestbook
        tier: frontend
        
while the Redis master and slave would have different tier labels, and perhaps even an additional role label:

     labels:
        app: guestbook
        tier: backend
        role: master
and

     labels:
        app: guestbook
        tier: backend
        role: slave
*/
//The labels allow us to slice and dice our resources along any dimension specified by a label:

$ kubectl get pods -Lapp -Ltier -Lrole
NAME                           READY     STATUS    RESTARTS   AGE       APP         TIER       ROLE
guestbook-fe-4nlpb             1/1       Running   0          1m        guestbook   frontend   <none>
guestbook-fe-ght6d             1/1       Running   0          1m        guestbook   frontend   <none>
guestbook-fe-jpy62             1/1       Running   0          1m        guestbook   frontend   <none>
guestbook-redis-master-5pg3b   1/1       Running   0          1m        guestbook   backend    master
guestbook-redis-slave-2q2yf    1/1       Running   0          1m        guestbook   backend    slave
guestbook-redis-slave-qgazl    1/1       Running   0          1m        guestbook   backend    slave
my-nginx-divi2                 1/1       Running   0          29m       nginx       <none>     <none>
my-nginx-o0ef1                 1/1       Running   0          29m       nginx       <none>     <none>
$ kubectl get pods -lapp=guestbook,role=slave
NAME                          READY     STATUS    RESTARTS   AGE
guestbook-redis-slave-2q2yf   1/1       Running   0          3m
guestbook-redis-slave-qgazl   1/1       Running   0          3m
```

### Updating annotations
``` shell
$ kubectl annotate pods my-nginx-v4-9gw19 description='my frontend running nginx'
$ kubectl get pods my-nginx-v4-9gw19 -o yaml
apiversion: v1
kind: pod
metadata:
  annotations:
    description: my frontend running nginx
...
```

### Scaling your application
``` shell
$ kubectl scale deployment/my-nginx --replicas=1
deployment.extensions/my-nginx scaled

//To have the system automatically choose the number of nginx replicas as needed, ranging from 1 to 3, do:
$ kubectl autoscale deployment/my-nginx --min=1 --max=3
horizontalpodautoscaler.autoscaling/my-nginx autoscaled
```

### In-place updates of resources
- kubectl apply
It is suggested to maintain a set of configuration files in source control (see configuration as code), so that they can be maintained and 
versioned along with the code for the resources they configure. Then, you can use kubectl apply to push your configuration changes to the cluster.

``` shell
$ kubectl apply -f https://k8s.io/examples/application/nginx/nginx-deployment.yaml
deployment.apps/my-nginx configured
```
Note: To use apply, ***always create resource initially with either 'kubectl apply' or 'kubectl create --save-config'***.

- kubectl edit
```
$ kubectl edit deployment/my-nginx
```

- kubectl patch

This command supports JSON patch, JSON merge patch, and strategic merge [patch](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#patch). 
[Update API Objects in Place Using kubectl patch](https://kubernetes.io/docs/tasks/run-application/update-api-object-kubectl-patch/)
``` shell
//Partially update a node using a strategic merge patch. Specify the patch as JSON.

kubectl patch node k8s-node-1 -p '{"spec":{"unschedulable":true}}'

//Partially update a node using a strategic merge patch. Specify the patch as YAML.

kubectl patch node k8s-node-1 -p $'spec:\n unschedulable: true'

//The following commands are equivalent:

kubectl patch deployment patch-demo --patch "$(cat patch-file.yaml)"
kubectl patch deployment patch-demo --patch 'spec:\n template:\n  spec:\n   containers:\n   - name: patch-demo-ctr-2\n     image: redis'

kubectl patch deployment patch-demo --patch "$(cat patch-file.json)"
kubectl patch deployment patch-demo --patch '{"spec": {"template": {"spec": {"containers": [{"name": "patch-demo-ctr-2","image": "redis"}]}}}}'


```
