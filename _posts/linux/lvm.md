###Create PV and exetend LV

After add a new disk partition, you can create a new PV.

    pvs      #list pvs
    pvcreate /dev/sdb1   #create new pv
    vgs      #list vgs
      VG     #PV   #LV    #SN   Attr   VSize   VFree
      centos   1     2      0    wz--n-  9.51g    0
    vgextend VGNAME DISKPART
    vgextend centos /dev/sdb1
    lvs  #list lvs
      LV   VG  Attr LSize Pool Origin Data% Meta% Move Log Cpy%Sync Convert
      root centos -wi-ao---- 8.51g 
      swap centos -wi-ao---- 1.00g
    lvextend -l +100%FREE /dev/centos/root
    resize2fs  /dev/centos/root

If all is fine , these steps will resize a VM's disk(after exec: qemu-img resize xxx.qcow2 +10G).

###Error
But 'root' partition is xfs format.Command 'resize2fs' execute on ext* format, an error occured.

    resize2fs: Bad Magic number in super-block while trying to open /dev/centos/root
    Couldn't find valid filesystem superblock
    
    df -hiT   #show filesystem type of disk
    
###How to fix this problem?
Because i extend disk with 10G(+10G), i need to reduce 'dev/centos/root' and remove '/dev/sdb1' from VG 'centos'.

    lvreduce -L -11G /dev/centos/root    #Reduce more than 10G to aviod not been reduced from lv
    vgreduce centos /dev/sdb1            #remove from VG 'centos'
    lvextend -l +100%FREE /dev/centos/root  #recover lv to it's origin size


###Actions after resize a data disk of VM

```
//for ext4 filesystem of linux
umount /dev/vdb
e2fsck -f /dev/vdb
resize2fs /dev/vdb
mount /dev/vdb /data

//for xfs filesystem of linux
umount /dev/vdb
xfs_repair /dev/vdb
mount /dev/vdb /data
xfs_growfs /data
```
