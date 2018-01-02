---
layout: post
title:  "configuration of hugepage for VM"
categories: linux
---

### hugepage configuration

Hugepage has pagesize of 2M or 1GB for x86 CPU.

- 2M pagesize

When boot a VM with hugepage memory through qemu, "-mem-path" can be used to configure hugepage mount point,
"qemu-system-x86_64 -m 1024 -smp 2 centos7.img -mem-path /dev/hugepages".

If a kernel support hugepage, after OS is booted, /dev/hugepages is mounted with 2M hugepage by default.
"mount | grep huge" 

Default pagesize of hugepage is 2M. It can be configured by "sysctl vm.nr_hugepages=num".
"cat /proc/meminfo | grep Huge" is used to list Hugepage info(1GB is not included).

- 1GB pagesize

Use "cat /proc/cpuinfo | grep pdpe1gb" to check whether a CPU support hugepage of 1GB pagesize.
"grep HUGETLB /boot/config-3.5.0" to check whether a kernel is support hugepage of 1GB.(output is something like CONFIG_HUGETLBFS=y CONFIG_HUGETLB_PAGE=y)

For 1GB hugepage, parameters should be add to cmdline of kernel before OS is booted:
```
root (hd0,0)
kernel (hd0,0)/boot/vmlinuz-3.5.0 ro root=/dev/sda1 hugepagesz=1GB hugepages=2 default_hugepagesz=1GB
initrd (hd0,0)/boot/initrd-3.5.0.img
```

"hugepagesz" is for size of HugeTLB memory page size. "hugepages" is the number of pages of 1GB memory.
"default_hugepagesz" is the default size of hugepagesize when mount a hugetlb file system with out a pagesize parameter.

After OS is booted, you can mount 1GB hugepage to a mount point or add it to fstab.
```
mount -t hugetlbfs hugetlbfs /dev/hugepages
mount -t hugetlbfs hugetlbfs /dev/hugepages -o pagesize=2MB 
mount -t hugetlbfs hugetlbfs /dev/hugepages1G -o pagesize=1048576k
```

### libvirt qemu configuration
When start a VM by libvirt, hugepage can be configured by libvirt xml.
"-mem-path" will be add to qemu by libvirt with "hugetlbfs_mount" in /etc/libvirt/qemu.conf been useed.
```
# by default hugetlbfs_mount = "/dev/hugepages" is used.
hugetlbfs_mount = ["/dev/hugepages", "/dev/hugepages1G"]
```

### debug
When hugetlbfs is not mounted or vm.nr_hugepages is 0, an error will reported:
```
"internal error: Unable to find any usable hugetlbfs mount for 1048576 KiB"

```
