### List disk partition

```
fdisk -l /dev/sdb
```
### Create new partition

```
fdisk /dev/sdb
Command (m for help):p  #show partioins
:n        #new a partion
Command action
  e extended
  p primary partition(1-4)
 p        #chose p 
 First Cylinder(xx-xxxx):    #choose default value
 Last ..................: +80G     #allocate 80G to this partition
 
 :w       #write  changes 
 
 #partprobe    #inform the OS of partition table changes
```

### Format partioin
```
mkfs.ext4 /dev/sdb1
#first time i allocate sdb1 as extended partition, while mkfs there are errors
#then i delete thart partition, and allocate as primary, mkfs works.
```
### Mount partition
```
mkdir /opt
mount /dev/sdb1 /opt

vim /etc/fstable :
  /dev/sdb1  /opt  ext4  defaults  0  0

```
