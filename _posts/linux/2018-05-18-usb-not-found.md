### mount a udisk to a VM

After attach a udisk to a VM, login as root user to check udisk, we found no disk is added.

Steps to solve problem:
```
fdisk -l
# show a sdb is found, it has a partition sdb1
lsusb
# shows a USB device is attached 

mount /dev/sdb1 /mnt
# error occurred, unknown file system: NTFS

lsusb
Bus 002 Device 004： ID 8653：1000 xxxx, Inc. Flash
# 8653：1000 is the ID of USB device 


```

The problem  is udisk is formatted as NTFS, but is not known by OS.

Format udisk with FAT32 would solve the problem.
