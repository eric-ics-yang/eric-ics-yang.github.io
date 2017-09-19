###Failed to start kvm with sriov

- vfio Operation not permitted

After add a sriov device to VM's interface, failed to start it with libvirtd.

```sh
error: internal error: process exited whileconnecting to monitor: 2015-08-11T06:11:06.627255Zqemu-kvm: -device vfio-pci,host=0e:00.0,id=hostdev0,bus=pci.2,addr=0x6: vfio:failed to open /dev/vfio/vfio: Operation not permitted
627315Z qemu-kvm: -devicevfio-pci,host=0e:00.0,id=hostdev0,bus=pci.2,addr=0x6: vfio: failed to setupcontainer for group 19
627331Z qemu-kvm: -devicevfio-pci,host=0e:00.0,id=hostdev0,bus=pci.2,addr=0x6: vfio: failed to get group19
627351Z qemu-kvm: -devicevfio-pci,host=0e:00.0,id=hostdev0,bus=pci.2,addr=0x6: Device initialization failed.
627371Z qemu-kvm: -devicevfio-pci,host=0e:00.0,id=hostdev0,bus=pci.2,addr=0x6: Device 'vfio-pci'  couldnot be initialized

Solution：
Add "/dev/vfio/vfio" to cgroup_device_acl in /etc/libvirt/qemu.conf.

Same solution to /dev/vfio/16 not permitted
```

- 直通了一张显卡，虚拟机不能开机

pci 0000:08:00.0 is not assignable

原因：qemu默认不能直通没有ACS(Access Control Service）控制的设备，要想直通这些设备，需要放开qemu的校验

/etc/libvirt/qemu.conf

开启relaxed_acs_check = 1的选项，这样这些设备就可以被虚拟机直通使用了

Ref:  [libvirt sriov blog]

  [libvirt sriov blog]: http://blog.csdn.net/qq123386926/article/details/47757089
