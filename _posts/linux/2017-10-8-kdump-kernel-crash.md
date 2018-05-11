### Writing booting log of OS to file in qemu

I ended up appending the following([serial]): -append console=ttyS0,38400 -serial file:serial.out

  [serial]:https://superuser.com/questions/269228/write-qemu-booting-virtual-machine-output-to-a-file

Redirect to a [file]

If you're using the QEMU virtual machine and want to redirect the serial output to a file, add the following to your QEMU command line:
```
-serial file:FILENAME
```

Replace FILENAME with the name of a file, into which the debug output shall be written.

  [file]:http://www.reactos.org/wiki/QEMU#Redirect_to_a_file

### command to crash

用root权限执行如下命令，可以让kernel crash。
``` shell
# echo 1 > /proc/sys/kernel/sysrq
# echo c > /proc/sysrq-trigger
```
kernel dump完成后，系统重启，进入到 /var/crash/ 目录下可以看到保存下来的crash时系统的内存数据文件。 （默认保存在/var/crash目录）

### 在CentOS 7 中配置kdump

需要在kernel启动命令行参数中添加crashkernel参数，并启动kdump服务。
```
... rhgb quiet crashkernel=auto
```
一般设置为 crashkernel=auto 表示根据系统内存自动reserve一些内存给kernelcrash用，在x86_64系统中内存大于等于2GB时会reserve内存，最小保留内存计算方法是：160 MB + 2 bits for every 4 KB of RAM.

当然，也可以设置crashkernel=512M这样的固定保留内存。

安装kdump工具和服务
```
yum install kexec-tools 
```

用 systemctl start kdump 命令可以启动kdump.service，用 systemctl enable kdump 让dump服务在开机时自动启动。

kdump.service 相关的配置文件 /etc/kdump.conf 里面可以修改一些默认的配置，比如dump完成后的动作（默认是reboot）、dump文件存放的方式（本地目录、NFS、scp到另外服务器等）。

CentOS is /etc/kdump.conf (in SLES operating system the path is /etc/sysconfig/kdump).
```
#net my.server.com:/export/tmp
#net user@my.server.com
path /var/crash
core_collector makedumpfile -c --message-level 1 -d 31
#core_collector scp
#core_collector cp --sparse=always
......
```

#### crash analysis
- View the Process when System Crashed

Execute ps command at the crash prompt, which will display all the running process when the system crashed.
```
crash> ps
```
- View Swap space when System Crashed
Execute swap command at the crash prompt, which will display the swap space usage when the system crashed.
```
crash> swap
FILENAME           TYPE         SIZE      USED   PCT  PRIORITY
/dm-1            PARTITION    2064376k       0k   0%     -1
```
- View IPCS when System Crashed

Execute ipcs command at the crash prompt, which will display the shared memory usage when the system crashed.

- View IRQ when System Crashed

Execute irq command at the crash prompt, which will display the IRQ stats when the system crashed.
```
crash> irq -s
           CPU0
  0:        149  IO-APIC-edge     timer
```
vtop – This command translates a user or kernel virtual address to its physical address.
foreach – This command displays data for multiple tasks in the system
waitq – This command displays all the tasks queued on a wait queue.

- View the Virtual Memory when System Crashed

Execute vm command at the crash prompt, which will display the virtual memory usage when the system crashed.

- View the Open Files when System Crashed

Execute files command at the crash prompt, which will display the open files when the system crashed.

- View System Information when System Crashed

Execute sys command at the crash prompt, which will display system information when the system crashed.
```
crash> sys
      KERNEL: /usr/lib/debug/lib/modules/2.6.32-431.5.1.el6.x86_64/vmlinux
    DUMPFILE: /var/crash/127.0.0.1-2014-03-26-12:24:39/vmcore  [ PARTIAL DUMP ]
        CPUS: 1
```

ref: [1] [2] [3] [4]

  [1]:https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Kernel_Crash_Dump_Guide/sect-kdump-config-cli.html#sect-kdump-config-cli-default-action
  [2]:http://www.tuicool.com/articles/RjUfE3n
    [3]: http://unixadminschool.com/blog/2015/07/configuring-kdump-to-troubleshoot-kernel-crashes-hangs-or-reboots-in-rhel5rhel6rhel7/#difference-between-chroot-pivot-root
  [4]: https://www.thegeekstuff.com/2014/05/kdump/
