### [转载] 记一次 superblock 损坏导致服务器无法启动的故障修复

[superblock lost] 比较典型的一个案例分析[1] [2] [3]。在此转载以警醒。


- 一、启动故障

系统无法启动，启动时内核panic：
```
Uncompressing Linux Ok, booting the kernel.
audit(1297269214.612:0): initialized
ide2: I/O resource 0x3F6-0x3F6 not free.
ide2: ports already in use, skipping probe
Red Hat nash version 4.1.18 starting
File descriptor 3 left open
  Reading all physical volumes.  This may take a while
  /dev/hda: open failed: No medium found
  Found volume group "VolGroup_ID_17253" using metadata type lvm2
File descriptor 3 left open
  8 logical volume(s) in volume group "VolGroup_ID_17253" now active
File descriptor 3 left open
VFS: Can't find ext3 filesystem on dev dm-0.
mount: error 22 mounting ext3
mount: error 2 mounting none
switchroot: mount failed: 22
umount /initrd/dev failed: 2
Kernel panic - not syncing: Attempted to kill init!
```

主机在重启之前在清理包含大量缓存文件的目录，想清理一下，于是用下面的命令删除这些生成的文件:
```
find . -name "*.php" -exec rm -f {} \;
```
等了几分钟，发现系统没有反应。于是就Ctrl-C了，后来发现系统还是很慢，于是就执行reboot了。接下来，系统就启动不起来了。

在如此密集磁盘写入时执行reboot，确实比较容易引起磁盘上的故障。

再加上这块硬盘虽然是ext3，但是日志使用的是默认的ordered [2]，出问题的几率就更大了，所以怀疑是 superblock 的可能性就更大了。

- 二、修复环境

    如果备份硬盘的话，最好找同样型号的服务器，同样的磁盘接口，同样的操作系统。比较好的办法是使用另一台具有SCSI接口的Linux，进行修复、挂载、备份。
还可以在原服务器上修复，但是使用 Linux LiveCD 进行修复，将数据备份到外置 USB 硬盘上。

    这里需要注意的是，即使硬盘有几个分区，只坏了一个，备份到其它貌似还好的分区似乎也不是什么大问题。
但是，在修复、备份的时候，一定要尽可能的避免被修复磁盘的写操作，无论是哪个分区。因为在问题确认修复前，你并不能肯定只有那个报错的分区有故障，其它一切正常。
    准备了 Fedora 14、Ubuntu 10.10、UBCD等光盘备用，并且打算使用 Fedora 14 的 Live CD 进行修复。
并且第二天带一个大容量的，最好是空的USB硬盘来，备份数据。并且，在家用 Fedora 14 启动一下，看看怎么进入***命令行模式***，第二天启动的时候，
不要进入图形界面。在命令行模式下修复。

在以前的某些 LiveCD 中，加载图形界面的时候，由于各种驱动和程序的加载，错误的进行了硬盘的写操作，从而导致有些人抱怨过启动 LiveCD 导致硬盘数据二次损坏，最终使得修复无望。
虽然，最近这些版本的 LiveCD 可能没有这类问题，但是为了避免万一，应当尽量减少对硬盘写入操作的可能性。既然所有修复行为都会在命令行模式下进行，那就没必要启动图形界面冒风险。

我让他带着 Ubuntu 的盘就是以防万一，万一 Fedora 启动出现奇怪的问题，我们可以用 Ubuntu 启动然后修复系统。
而不是进入 Ubuntu的修复系统选项。那个选项是给 Ubuntu 系统准备的，是以光盘上的系统文件及配置覆盖硬盘上的系统文件及配置，从而达到修复系统的目的。

- 三、确认问题

首先我需要知道，都有哪些文件系统被他挂载了，另外，系统上有哪些分区。
```
[root@localhost liveuser]# mount |grep LogVol
/dev/mapper/VolGroup_ID_17253-LogVol4 on /media/0edef924-567f-45fc-9609-51722cad6e9e type ext3 (rw,nosuid,nodev,uhelper=udisks)
/dev/mapper/VolGroup_ID_17253-LogVol7 on /media/ee0c40c6-d9d1-4a81-9806-9991621db1dd type ext3 (rw,nosuid,nodev,uhelper=udisks)
/dev/mapper/VolGroup_ID_17253-LogVolHome on /media/f524534e-3d24-4a22-b475-9e4b7dac0d35 type ext3 (rw,nosuid,nodev,uhelper=udisks)
/dev/mapper/VolGroup_ID_17253-LogVol6 on /media/12953c57-baba-4358-baeb-cdd17d6513a2 type ext3 (rw,nosuid,nodev,uhelper=udisks)
```
据我所知，服务器上好像有5个绑定目录的分区，LogVol{3,4,6,7,Home}，LogVol3 好像就是那个坏的分区，想绑也绑不上，除了它，他全绑上了。

我需要确定 LVM 总共有哪些分区，lvscan 命令可以告诉我们LVM下面的分区情况。
```
[root@localhost liveuser]# lvscan
ACTIVE '/dev/VolGroup_ID_17253/LogVol3' [10.00 GiB] inherit
ACTIVE '/dev/VolGroup_ID_17253/LogVol4' [1.06 GiB] inherit
ACTIVE '/dev/VolGroup_ID_17253/LogVol7' [53.56 GiB] inherit
ACTIVE '/dev/VolGroup_ID_17253/LogVol6' [5.38 GiB] inherit
ACTIVE '/dev/VolGroup_ID_17253/LogVol1' [2.00 GiB] inherit
ACTIVE '/dev/VolGroup_ID_17253/LogVol0' [2.00 GiB] inherit
ACTIVE '/dev/VolGroup_ID_17253/LogVol2' [64.00 MiB] inherit
ACTIVE '/dev/VolGroup_ID_17253/LogVolHome' [29.44 GiB] inherit
```
嗯，LogVol{0,1}是交换分区，LogVol2肯定不是我们的目标，那么缺失的是LogVol3，这个分区出问题了。如此，我们手动绑定一下 LogVol3 看一下报错信息。
```
[root@localhost liveuser]# mkdir /media/myroot
[root@localhost liveuser]# mount -t ext3 /dev/mapper/VolGroup_ID_17253-LogVol3 /media/myroot
mount: wrong fs type, bad option, bad superblock on /dev/mapper/VolGroup_ID_17253-LogVol3,
missing codepage or helper program, or other error
In some cases useful info is found in syslog - try
dmesg | tail or so
```
这里我们可以直接看到报错说 'bad superblock' 信息。
然后我们再看一下内核报错。
```
[root@localhost liveuser]# dmesg | tail -n 20
[ 343.583694] EXT3-fs (dm-3): mounted filesystem with ordered data mode
[ 343.585926] SELinux: initialized (dev dm-3, type ext3), uses xattr
[ 346.179128] EXT3-fs: barriers not enabled
[ 346.183702] kjournald starting. Commit interval 5 seconds
[ 346.189688] EXT3-fs (dm-4): using internal journal
[ 346.189694] EXT3-fs (dm-4): mounted filesystem with ordered data mode
[ 346.193216] SELinux: initialized (dev dm-4, type ext3), uses xattr
[ 348.911539] EXT3-fs: barriers not enabled
[ 348.918113] kjournald starting. Commit interval 5 seconds
[ 348.918151] EXT3-fs (dm-9): warning: mounting fs with errors, running e2fsck is recommended
[ 348.922722] EXT3-fs (dm-9): using internal journal
[ 348.922728] EXT3-fs (dm-9): mounted filesystem with ordered data mode
[ 348.922738] SELinux: initialized (dev dm-9, type ext3), uses xattr
[ 350.225535] EXT3-fs: barriers not enabled
[ 350.230730] kjournald starting. Commit interval 5 seconds
[ 350.236075] EXT3-fs (dm-5): using internal journal
[ 350.236081] EXT3-fs (dm-5): mounted filesystem with ordered data mode
[ 350.241386] SELinux: initialized (dev dm-5, type ext3), uses xattr
[ 1957.796112] EXT3-fs (dm-2): error: can't find ext3 filesystem on dev dm-2.
[ 2688.247855] EXT3-fs (dm-2): error: can't find ext3 filesystem on dev dm-2.
```
这里我们看到，内核报错说无法在 dm-2 上找到 ext3 文件系统。这很有可能就是 superblock 损坏造成的问题。

另外，我们看到，正如我所担心的那样，不仅仅是 dm-2 (LogVol3) 有故障， dm-9 分区也有故障。很可能其它分区也有问题，都需要在使用前，进行磁盘检查 fsck。冒失的访问，很可能会造成数据损坏或丢失。

如此，我们基本上可以确定是 superblock 的损坏，至于是否还有其它故障，以及是否有数据损失，需要在 fsck 之后才能知道了。


- 四、镜像备份损坏的硬盘

执行 fsck 会对磁盘进行写操作，我们需要在此之前对磁盘进行镜像备份。这样万一 fsck 的修复造成了更大的损失，我们还可以恢复原始状态。
通过 mount 命令找到新绑定的磁盘路径：
```
/dev/sdb1 on /media/BACKUP type fuseblk (rw,nosuid,nodev,allow_other,blksize=4096,default_permissions)
```
然后，开始镜像 LogVol3：
```
[root@localhost ~]# dd if=/dev/VolGroup_ID_17253/LogVol3 | gzip > /media/BACKUP/server_root_image.gz
20971520+0 records in
20971520+0 records out
10737418240 bytes (11 GB) copied, 666.429 s, 16.1 MB/s
```
确认一下文件确实存在：
```
[root@localhost ~]# ls -l /media/BACKUP/*.gz
-rwxrwxrwx. 1 liveuser liveuser 5943229016 Feb 10 17:29 /media/BACKUP/server_root_image.gz
```
- 五、修复

先进行第一次修复尝试。
```
[root@localhost liveuser]# fsck.ext3 -B 1024 /dev/mapper/VolGroup_ID_17253-LogVol3
e2fsck 1.41.12 (17-May-2010)
fsck.ext3: Superblock invalid, trying backup blocks
fsck.ext3: Bad magic number in super-block while trying to open /dev/mapper/VolGroup_ID_17253-LogVol3

The superblock could not be read or does not describe a correct ext2
filesystem. If the device is valid and it really contains an ext2
filesystem (and not swap or ufs or something else), then the superblock
is corrupt, and you might try running e2fsck with an alternate superblock:
e2fsck -b 8193 <device>
```
这里面说无法修复，原因是 superblock 损坏了，所以 fsck 无法定位相关分区数据。建议使用备份的 superblok。

我们知道，superblock 对于分区而言非常重要，因此 ext2/3 文件系统将 superblock 备份到了磁盘的各个位置，如此多的备份，降低了所有 superblock 备份都损坏的概率。

可是问题是，这些备份在哪里呢？superblock 的备份是和 block size 相关的。询问后得知，这个服务器上的分区的参数都是默认设置，只是调整了一下大小和个数。既然如此，那么所有的分区都应该是同样的 block size，那么它们备份 superblock 的相对位置也都一样。

鉴于此，我们打算通过 LogVol7 分区给出一个superblock 备份相对位置的列表：
```
[root@localhost liveuser]# dumpe2fs /dev/VolGroup_ID_17253/LogVol7 | grep -i superblock
dumpe2fs 1.41.12 (17-May-2010)
Primary superblock at 0, Group descriptors at 1-4
Backup superblock at 32768, Group descriptors at 32769-32772
Backup superblock at 98304, Group descriptors at 98305-98308
Backup superblock at 163840, Group descriptors at 163841-163844
Backup superblock at 229376, Group descriptors at 229377-229380
Backup superblock at 294912, Group descriptors at 294913-294916
Backup superblock at 819200, Group descriptors at 819201-819204
Backup superblock at 884736, Group descriptors at 884737-884740
Backup superblock at 1605632, Group descriptors at 1605633-1605636
Backup superblock at 2654208, Group descriptors at 2654209-2654212
```
尝试使用 32768 的备份：
```
[root@localhost liveuser]# fsck.ext3 -B 1024 -b 32768 /dev/mapper/VolGroup_ID_17253-LogVol3
e2fsck 1.41.12 (17-May-2010)
fsck.ext3: Bad magic number in super-block while trying to open /dev/mapper/VolGroup_ID_17253-LogVol3

The superblock could not be read or does not describe a correct ext2
filesystem. If the device is valid and it really contains an ext2
filesystem (and not swap or ufs or something else), then the superblock
is corrupt, and you might try running e2fsck with an alternate superblock:
e2fsck -b 8193 <device>
```

这个备份还是不行，再换一个：
```
[root@localhost liveuser]# fsck.ext3 -b 98304 /dev/VolGroup_ID_17253/LogVol3
e2fsck 1.41.12 (17-May-2010)
Superblock needs_recovery flag is clear, but journal has data.
Recovery flag not set in backup superblock, so running journal anyway.
/dev/VolGroup_ID_17253/LogVol3: recovering journal
Adding dirhash hint to filesystem.

Pass 1: Checking inodes, blocks, and sizes
Inode 81, i_blocks is 8, should be 0. Fix<y>?
```
不错，98304 这个备份是好的。已经修复了一些内容了，只要继续就很有可能修复系统。

重新开始修复硬盘：
```
[root@localhost liveuser]# fsck.ext3 -y -b 98304 /dev/VolGroup_ID_17253/LogVol3



Free blocks count wrong for group #78 (32254, counted=5049).
Fix? yes

Free blocks count wrong for group #79 (32254, counted=4724).
Fix? yes
```

经过了一段时间的等待，LogVol3 修复终于完成了。由于得知当时LogVolHome进行了大量的读写，因此，虽然可以挂载，但是非常怀疑其中也有故障，因此也许进行磁盘检查和修复：
```
[root@localhost /]# fsck.ext3 /dev/VolGroup_ID_17253/LogVolHome
e2fsck 1.41.12 (17-May-2010)
/dev/VolGroup_ID_17253/LogVolHome contains a file system with errors, check forced.
Pass 1: Checking inodes, blocks, and sizes
Pass 2: Checking directory structure
Pass 3: Checking directory connectivity
Pass 4: Checking reference counts
Pass 5: Checking group summary information
/dev/VolGroup_ID_17253/LogVolHome: 2301889/3859072 files (9.9% non-contiguous), 2554717/7716864 blocks
```
果然，确实有错误，并且修复了。

然后，尝试挂载 LogVol3，看这次是否没有问题了：
```
[root@localhost /]# mkdir /media/myroot
[root@localhost /]# mount -t ext3 /dev/VolGroup_ID_17253/LogVol3 /media/myroot
```
一切正常，没有任何报错。磁盘修复基本可以宣告完成，接下来就是备份和重新启动了。

- 六、备份重要数据

重新启动系统，如果一切正常，系统会正常加载所有的服务器，并且开始提供服务，那时数据就会发生改变了，在还不知道服务器是否正常的情况下贸然启动服务器，而没有备份，这是危险的。因此，我们先备份重要数据。
```
[root@localhost /]# cd /media/myroot
[root@localhost myroot]# tar -czvf /media/BACKUP/www.tgz www
[root@localhost myroot]# tar -czvf /media/BACKUP/server_lampp.tgz opt/lampp
[root@localhost myroot]# tar -czvf /media/BACKUP/server_mysql.tgz opt/lampp/var/mysql
```

最后确认一下数据是否已经备份好了。
```
[root@localhost myroot]# ls l /media/BACKUP
total 6287380
-rwxrwxrwx. 1 liveuser liveuser 92690926 Feb 10 19:35 server_lampp.tgz
-rwxrwxrwx. 1 liveuser liveuser 28670158 Feb 10 19:30 server_mysql.tgz
-rwxrwxrwx. 1 liveuser liveuser 5943229016 Feb 10 17:29 server_root_image.gz
-rwxrwxrwx. 1 liveuser liveuser 373677732 Feb 10 19:34 www.tgz
```

```
/dev/VolGroup_ID_17253/LogVolHome:
 Inode 1340876 is too big.
 
/dev/VolGroup_ID_17253/LogVolHome: UNEXPECTED INCONSISTENCY; RUN fsck MANUALLY.
        (i.e., without -a or -p options)
                                                           [FAILED]

*** An error occurred during the file system check.
*** Dropping you to a shell; the system will reboot
*** when you leave the shell.
*** Warning -- SELinux is active
*** Disabling security enforcement for system recovery.
*** Run 'setenforce 1' to reenable.
Give root password for maintenance
(or type Control-D to continue): _
```
发现这是 e2fsprogs，也就是 fsck.ext3 所在包，早期版本的一个bug。如果inode节点很大，会触发这个bug。1.40以后就没有这个问题了

这也看出来我一直坚持要升级的原因之一。越早的版本的软件包含的bug就越多，每年新的版本都会修复大量的bug。
而那些坚持版本越老越稳定的人，实际上是非常错误的。太新和太老都是不合理的，要取一个合适的折中。像 RHEL 4，甚至5，就太老了。

其内很多版本包含了大量的bug，可是由于没有升级到新的版本，RedHat不得不花大力气去将修复bug的补丁打到老的软件上，而有相当数量的补丁是完全基于新版本API的，那些补丁就没有办法打在老软件上。
RHEL/CentOS所谓的长期稳定，也就是因为RedHat有大量的人力在做这种将新软件的补丁打到老软件上的事情。可是，有些时候，我们直接用新的版本会更好。

我没心思在LiveCD里面升级e2fsprogs，这是个危险的操作。触发这个bug的条件是一个目录下的文件太多，从而导致inode过大。
了解了一下，那个目录正好是服务器故障前正在清理的目录。那么我们就先把这个目录清理掉再说。

清理后，就可以正常启动了，不会触发那个bug了。

- 八、后记

这次系统故障导致我朋友很紧张的原因之一就是系统没有备份。

其实使用Linux备份还是比较容易的。最简单的办法是用crontab，定义的压缩一份重要数据，传到别的服务器上去，或者复制到别的物理硬盘上。

我们经常看到类似于，“什么服务器最安全快速？”、“什么编程语言效率最高？”的问题。这类问题实际上问题本身就有问题。没有最安全的服务器，没有最好的编程语言，只有最合适你的。

无论哪个系统，都可以配置出安全、高效的服务器。

但是，你得精心配置啊。

安全界上，凡是默认配置的，基本都是有隐患的。大多数入侵啊、蠕虫啊，也都是针对默认配置的机器做的。不管你是 Linux 也好，Windows 也罢。

只要是默认配置，你就不要提什么谁更安全，那是扯淡。

还有一个问题就是升级。这个服务器用的是 RHEL，貌似国内好像比较喜欢用这个。虽然其内的软件很老，但是这个发布确实比较稳定，你要是用正版的，有售后支持，我也觉得没什么。

国外用RHEL的人多，那是冲着售后去的，人家有24小时应急响应，服务器出了问题很快就能解决。

哪怕你用CentOS呢，虽然没有售后，但起码可以升级啊。

今天出现的问题，说不定什么时候就又会出现。问题需要进一步的挖掘，才能真正的让服务器，健康的运行.




  [superblock lost]: http://www.cppblog.com/dancefire/archive/2011/03/09/fix-bad-superblock-in-linux.html
  [1]: http://www.cyberciti.biz/tips/understanding-unixlinux-filesystem-superblock.html
  [2]: http://en.wikipedia.org/wiki/Ext3#Journaling_levels
  [3]: http://www.ibm.com/developerworks/cn/linux/filesystem/lvm/lvm-1/index.html
