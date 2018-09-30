### etcd is down after running for some uncertain time

- Problem 

I deploy a kubernetes cluster with 3 VM(4C8G). After running for some uncertain time (one day or just 1 hour ) the k8s service is not available.  
At first, i found the etcd and apiserver container is down and been start by kubelet. I try to stop kubelet service, docker service, and start docker then kubelet service.

- First try

This solution does not work everytime, sometimes i need to repeat the procure for three times more, sometimes i need to reboot the VM which host
etcd container.

- Checking log

When checking the log of messages, nothingness useful seems found.

When i check the log of etcd container with "docker logs ETCD-CONTAINER-ID" no error log is found, but etcd is been restarted by kubelet 
on and on.

- Fixed

After been troubled by the problem for a week, all a sudden i realize the performance may be the problem.  
I check the yaml of etcd pod, found **"initialDelaySeconds"** and **"timeoutSeconds"** of etcd is just 15, I try to update those value to 60，  
and do the procedure above to restart k8s cluster service.

THE problem fixed. :)

