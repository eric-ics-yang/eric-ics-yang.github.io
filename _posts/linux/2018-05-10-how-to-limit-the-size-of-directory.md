### how-to limit the size of directory

Use file as a [loop device]
```
dd if=/dev/zero of=file.disk bs=1M count=50

losetup /dev/loop100 file.disk

mkfs.ext3 /dev/loop100

losetup -d /dev/loop100

mount -o loop file.disk ./dirSizeLimitted

losetup -l loop2

```

  [loop device]:https://blog.csdn.net/o4dc8ojo7zl6/article/details/78496494
  
### Can we have directory [quota] on Linux?
Great solution for a quick creation of quoted directories (for samba shares for example). 
This even allows for free space monitoring in the directories by Nagios or alike if needed. Thanks a lot folks!

Just wanted to sum things up, I had to dig around a bit to find the right commands.
```
1. create the mount point
Code:
# mkdir /var/virtual_disks/directory_with_size_limit
2. create a file full of /dev/zero, large enough to the maximum size you want to reserve for the virtual file-system
Code:
# touch /var/virtual_disks/directory_with_size_limit.ext3
# dd if=/dev/zero of=/var/virtual_disks/directory_with_size_limit.ext3 bs=QUOTA_SIZE count=1
3. format this file with an ext3 file-system (you can format a disk space even if it is not a block device, but double check the syntax of every - dangerous - formatting command)
Code:
# mkfs.ext3 /var/virtual_disks/directory_with_size_limit.ext3
4. mount the newly formatted disk space in the directory you've created as mount point, e.g.
Code:
# mount -o loop,rw,usrquota,grpquota /var/virtual_disks/directory_with_size_limit.ext3 /path/of/mount/point
As a result you now have a directory in /path/of/mount/point with a size limitation.

If you wish to add more space to (trim the size of) the directory:
Code:
# umount /path/of/mount/point

# e2fsck -f /var/virtual_disks/directory_with_size_limit.ext3

# resize2fs -p /var/virtual_disks/directory_with_size_limit.ext3 NEW_SIZE

# mount -o loop,rw,usrquota,grpquota /var/virtual_disks/directory_with_size_limit.ext3 /path/of/mount/point


You add a line to the /etc/fstab file. For example:

Code:
/var/virtual_disks/directory_with_size_limit.ext3  /path/of/mount/point  ext3   0  1
Here,

* /var/virtual_disks/directory_with_size_limit.ext3 is the file you created containing the new file system
* /path/of/mount/point is the directory that you want to use and that should have limited size.
```



  [quota]:https://www.linuxquestions.org/questions/linux-server-73/directory-quota-601140/#post4385296
