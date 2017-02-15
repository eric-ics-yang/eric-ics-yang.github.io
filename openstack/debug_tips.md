###安全组相关命令：

```sh
nova secgroup-add-rule default tcp 22 22 0.0.0.0/0  ---设置虚拟机能ssh
nova secgroup-add-rule default icmp -1 -1 0.0.0.0/0  ---设置虚拟机能ping icmp包
nova secgroup-list
nova secgroup-list-rules default 
```
