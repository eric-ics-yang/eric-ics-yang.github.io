### command to crash

    用root权限执行如下命令，可以让kernel crash。
``` shell
# echo 1 > /proc/sys/kernel/sysrq
# echo c > /proc/sysrq-trigger
```
kernel dump完成后，系统重启，进入到 /var/crash/ 目录下可以看到保存下来的crash时系统的内存数据文件。 （默认保存在/var/crash目录）

### 在CentOS 7 中配置kdump

需要在kernel启动命令行参数中添加crashkernel参数，并启动kdump服务。

一般设置为 crashkernel=auto 表示根据系统内存自动reserve一些内存给kernelcrash用，在x86_64系统中内存大于等于2GB时会reserve内存，最小保留内存计算方法是：160 MB + 2 bits for every 4 KB of RAM.

当然，也可以设置crashkernel=512M这样的固定保留内存。

用 yum install kexec-tools 安装kdump工具和服务

用 systemctl start kdump 命令可以启动kdump.service，用 systemctl enable kdump 让dump服务在开机时自动启动。

kdump.service 相关的配置文件 /etc/kdump.conf 里面可以修改一些默认的配置，比如dump完成后的动作（默认是reboot）、dump文件存放的方式（本地目录、NFS、scp到另外服务器等）。


ref: [1] [2]

  [1]:https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Kernel_Crash_Dump_Guide/sect-kdump-config-cli.html#sect-kdump-config-cli-default-action
  [2]:http://www.tuicool.com/articles/RjUfE3n
