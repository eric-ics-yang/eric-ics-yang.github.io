###security group：

```sh
nova secgroup-add-rule default tcp 22 22 0.0.0.0/0  ---all ssh into vms
nova secgroup-add-rule default icmp -1 -1 0.0.0.0/0  ---allow ping icmp to vms
nova secgroup-list 
nova secgroup-list-rules default 
```

### Cinder volume type

```sh
cinder type-create example_type
cinder type-key example_type set volume_backend_name=example_backendname
cinder extra-specs-list
+--------------------------------------+------+-------------------------------------------------+
|                  ID                  | Name |            extra_specs                          |
+--------------------------------------+------+-------------------------------------------------+
| 1a0cb997-58a6-43bf-axad-199e0e02239b | sata | {u'volume_backend_name': u'example_backendname'}|
+--------------------------------------+------+-------------------------------------------------+


```
