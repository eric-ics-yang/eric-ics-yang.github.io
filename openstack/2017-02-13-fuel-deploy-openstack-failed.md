---
layout:     live_for_work
title:      Problem about OpenStack deployment with fuel
category:   openstack
description: Reason and Problem about fuel failed to deploy OpenStack
---


###Problem

We write a fuel plugin for MOS 9.0 (OpenStack Mitaka), last week it failed to deploy a OpenStack Env with fuel while a doing a test before release.

Tried about 5 times, even with a configuration without our plugin code enabled, still failed.

Error message is different and strange, "keystone user login from xxx with password failed", "retrive glance info failed ", "Not managing Keystone_role[_member_] due to earlier Keystone API failures", "Cloud not perfetch neutron_network provider 'neutron': Can't retrieve net-list because Neutron or Keystone API is not available."
"pacemaker Pcmk_resource ... ensured failed", even got error message from rabbitmq.

###Solution

After times of try and fail, it seems network is also slow, the process of install takes more time than normal.

There two fuel environment in our network, maybe it's been affected by another fuel.

After confirmed with network staff, it's seems that the configuration of management and pxe network are the same, **same CIDR address** and **same VLAN tag**.

Unfortunately, the physical network of two fuel environment is **reachable**.

So the keystone , mysql, rabbitmq in the two fuel environment can see each other, they got confused.

I change CIDR and VLAN tag for management and storage in one fuel env, and make deployment again, it **success**!
