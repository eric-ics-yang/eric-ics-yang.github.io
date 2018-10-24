### Linux Continuous Integration without VM Support

Example with kubectl [installation](https://github.com/kubernetes/minikube#linux-continuous-integration-without-vm-support):
```shell
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 && chmod +x minikube && sudo cp minikube /usr/local/bin/ && rm minikube
curl -Lo kubectl https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl && chmod +x kubectl && sudo cp kubectl /usr/local/bin/ && rm kubectl

export MINIKUBE_WANTUPDATENOTIFICATION=false
export MINIKUBE_WANTREPORTERRORPROMPT=false
export MINIKUBE_HOME=$HOME
export CHANGE_MINIKUBE_NONE_USER=true
mkdir -p $HOME/.kube
mkdir -p $HOME/.minikube
touch $HOME/.kube/config

export KUBECONFIG=$HOME/.kube/config
sudo -E minikube start --vm-driver=none

# this for loop waits until kubectl can access the api server that Minikube has created
for i in {1..150}; do # timeout for 5 minutes
   kubectl get po &> /dev/null
   if [ $? -ne 1 ]; then
      break
  fi
  sleep 2
done

# kubectl commands are now able to interact with Minikube cluster
```

### access dashboard through IP of VM

When run minikube in a VM with "minikube start --vm-driver=none", " minikube dashboard --url" output is: "http://10.0.2.15:30000", which is the IP of one network interface .  
To access dashboard through IP of another network interface, we can setup a [NAT](http://blog.51cto.com/wangpengtai/2131892).  
Add 

> net.ipv4.ip_forward= 1

to "/etc/sysctl.conf"
> sysctl -p

> iptables -t nat -A POSTROUTING -j MASQUERADE

Then we can access dashboard by "http://192.168.56.101:30000"


### kubernetes-dashboard is CrashLoopBackOff 

The log of kubernetes-dashboard is:
```
2018/10/24 08:53:59 Starting overwatch
2018/10/24 08:53:59 Using in-cluster config to connect to apiserver
2018/10/24 08:53:59 Using service account token for csrf signing
2018/10/24 08:53:59 No request provided. Skipping authorization
2018/10/24 08:53:59 Successful initial request to the apiserver, version: v1.10.0
2018/10/24 08:53:59 Generating JWE encryption key
2018/10/24 08:53:59 New synchronizer has been registered: kubernetes-dashboard-key-holder-kube-system. Starting
2018/10/24 08:53:59 Starting secret synchronizer for kubernetes-dashboard-key-holder in namespace kube-system
2018/10/24 08:53:59 Synchronizer kubernetes-dashboard-key-holder-kube-system exited with error: unexpected object: &Secret{ObjectMeta:k8s_io_apimachinery_pkg_apis_meta_v1.ObjectMeta{Name:,GenerateName:,Namespace:,SelfLink:,UID:,ResourceVersion:,Generation:0,CreationTimestamp:0001-01-01 00:00:00 +0000 UTC,DeletionTimestamp:<nil>,DeletionGracePeriodSeconds:nil,Labels:map[string]string{},Annotations:map[string]string{},OwnerReferences:[],Finalizers:[],ClusterName:,Initializers:nil,},Data:map[string][]byte{},Type:,StringData:map[string]string{},}
2018/10/24 08:53:59 Restarting synchronizer: kubernetes-dashboard-key-holder-kube-system.
2018/10/24 08:53:59 Starting secret synchronizer for kubernetes-dashboard-key-holder in namespace kube-system
2018/10/24 08:53:59 Synchronizer kubernetes-dashboard-key-holder-kube-system exited with error: kubernetes-dashboard-key-holder-kube-system watch ended with timeout
2018/10/24 08:53:59 Restarting synchronizer: kubernetes-dashboard-key-holder-kube-system.
2018/10/24 08:53:59 Starting secret synchronizer for kubernetes-dashboard-key-holder in namespace kube-system
2018/10/24 08:53:59 Synchronizer kubernetes-dashboard-key-holder-kube-system exited with error: kubernetes-dashboard-key-holder-kube-system watch ended with timeout
```

I do the follow steps to restart a minikube setup.
```shell
minikube stop
minikube delete
rm -fr /etc/kubernetes/
mv .minikube .minikube.bak
rm -fr .minikube
cp -r .minikube.bak/cache/ .minikube

#maybe this is the problem.
#I try those steps two or more times, last try i erase content of config， then CrashLoopBackOff problem fixed.
mv .kube/config  tmp/
touch .kube/config

#before run minikube, check kubelet and docker status
systemctl status kubelet
systemctl status docker

minikube start --vm-driver=none

```

```
#tree  .minikube
.minikube
├── cache
│   ├── images
│   ├── iso
│   ├── localkube
│   └── v1.10.0
│       ├── kubeadm
│       └── kubelet


#cat .kube/config
apiVersion: v1
clusters:
- cluster:
    certificate-authority: /root/.minikube/ca.crt
    server: https://10.0.2.15:8443
  name: minikube
contexts:
- context:
    cluster: minikube
    user: minikube
  name: minikube
current-context: minikube
kind: Config
preferences: {}
users:
- name: minikube
  user:
    client-certificate: /root/.minikube/client.crt
    client-key: /root/.minikube/client.key


#kubectl -n kube-system get pod 
NAME                                    READY     STATUS        RESTARTS   AGE
etcd-minikube                           1/1       Running       14         39d
kube-addon-manager-minikube             1/1       Running       24         44d
kube-apiserver-minikube                 1/1       Running       15         39d
kube-controller-manager-minikube        1/1       Running       14         39d
kube-dns-86f4d74b45-w9m8c               3/3       Running       73         44d
kube-proxy-q27f8                        1/1       Running       26         49d
kube-scheduler-minikube                 1/1       Running       14         39d
kubernetes-dashboard-5498ccf677-w27b6   0/1       Terminating   1429       44d
kubernetes-dashboard-5498ccf677-xdhkc   0/1       Error         1          8s
storage-provisioner                     1/1       Running       27         40d

```
