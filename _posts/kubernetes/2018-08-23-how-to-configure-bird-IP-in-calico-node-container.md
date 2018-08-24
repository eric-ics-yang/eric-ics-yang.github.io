### How to configure IP used by bird in calico/node container?

When deploy Calico with DaemonSet in Kubernetes, K8S will start a container on every node.  
By default, Calico will use IP detected automatically from IP configuration of host, the first valid IP address (seems like minimum IP) will be selected if there are more than one IP on host.  
When Harbor is installed on node, there is a IP like "10.18.x.x" bridge installed on it, the actual IP of node is "192.168.x.x". When calico/node container is loaded by DaemonSet, the configuration of container on every node is the same. The node which has harbor installed on it has two IP to be chosen from, unfortunately "10.18.x.x" is been chosen by calico/node. 

If we have three node: "192.168.x.1", "192.168.x.2", "192.168.x.3", Harbor is installed on "192.168.x.2". When calino/node is started on every node. We can see output of "calicoctl node status" of three node is :
```
# on 192.168.x.3
Calico process is running.

IPv4 BGP status 
---------------------------------------------------
|PEER ADDRESS |  PEER TYPE| STATE | SINCE| INFO |
---------------------------------------------------
|10.18.x.x   | node-to-node mesh| start 01:09:06| Connect|
-------------------------------------------------------------
|192.168.x.1| node-to-node mesh| up01:09:06| Established|
--------------------------------------------------------------

# on 192.168.x.1
Calico process is running.

IPv4 BGP status 
---------------------------------------------------
|PEER ADDRESS |  PEER TYPE| STATE | SINCE| INFO |
---------------------------------------------------
|10.18.x.x   | node-to-node mesh| start 01:09:06| Connect|
-------------------------------------------------------------
|192.168.x.3| node-to-node mesh| up01:09:06| Established|
--------------------------------------------------------------

# on 192.168.x.2
Calico process is running.

IPv4 BGP status 
---------------------------------------------------
|PEER ADDRESS |  PEER TYPE| STATE | SINCE| INFO |
---------------------------------------------------
|192.168.x.1   | node-to-node mesh| start| 01:09:06| Active Socket: Connection reset by peer|
-------------------------------------------------------------
|192.168.x.3| node-to-node mesh| start| 01:09:06| Active Socket: Connection reset by peer|
--------------------------------------------------------------

```
- Try "IP_AUTODETECTION_METHOD" 
Calico is not setup  correctly on node which has Harbor, first we can see it's about IP detected automatically.  
The  "first-found" IP Autodetection is been used for IP configuration of Calico.  
We can set "IP_AUTODETECTION_METHOD" of Calico to fix this problem.

> IP_AUTODETECTION_METHOD=interface=eth.*  (# Valid IP address on interface eth0, eth1, eth2 etc.)

> kubectl -n kube-system edit ds calico-node

We add " IP_AUTODETECTION_METHOD=interface=eth.*" to  "Env" of pod spec in DaemonSet like "CALICO_IPV4POOL_IPIP=always", "IP=".

Unfortunately, after delete pod of calico/node on node(k8s will restart it automatically ) , the problem is still there.  
I try to restart all three calico/node pod, but still don't work.

```
IP	The IPv4 address to assign this host or detection behavior at startup. 
Refer to IP setting for the details of the behavior possible with this field.	
```
"IP_AUTODETECTION_METHOD" is a key on [config] of Calico used to configure the way IP been detected.  

- Try to edit bird.cfg

Log in calico/node container, with command "ps -ef | grep calico", we can see a process is started with  
configuration "bird -R -s /var/run/calico/bird.ctl -d -c /etc/calico/confd/config/bird.cfg".
```
router id 192.168.x.2
...

# For peer /host/xxx/ip_addr_v4
protocol bgp Mesh_192_168_x_1 from bgp_template {
   neighbor 192.168.x.1 as 64512;
}
# ...
# Skipping ourseleves (192.168.x.2)
...

```
It seems like bird is started with bird.cfg, edit bird.cfg update "router id" may be useful.  
After edit that file, we commit this change, tag it as the origin calico/node tag. We restart the pod on  
this node. The result is bad.


- Try to set IP in Env

Finally, we have no idea of how to update bird.cfg in calico/node container. We go for [startup] on github, some code seems like 
the thing what we are just looking for.
```
oldIpv4 := node.Spec.BGP.IPv4Address

ipv4Env := os.Getenv("IP")

```
An idea is to set "IP" in "Env", because our pod is controlled by DaemonSet, "IP" differ from each other on nodes, we cannot edit DaemonSet.  
So we have to change container image on the node which have more than one IP.

> export IP="192.168.x.2"

The statement above is added to "start_runit" command. We commit current container to a image, and tag it as the calico/node image been used by DaemonSet.
After restart the pod, the problem is solved finally.


  [startup]:https://github.com/projectcalico/node/blob/master/pkg/startup/startup.go#L381
  [config]:https://docs.projectcalico.org/v3.2/reference/node/configuration
