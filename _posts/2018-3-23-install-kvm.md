### Check envirionment

To run KVM, you need a processor that supports hardware virtualization. 

Intel and AMD both have developed extensions for their processors, deemed respectively Intel VT-x (code name Vanderpool) and AMD-V (code name Pacifica). 
[check before install]
```
#egrep -c '(vmx|svm)' /proc/cpuinfo
If 0 it means that your CPU doesn't support hardware virtualization.
If 1 or more it does - but you still need to make sure that virtualization is enabled in the BIOS.

To see if your processor is 64-bit, you can run this command:

#egrep -c ' lm ' /proc/cpuinfo

If 0 is printed, it means that your CPU is not 64-bit.
If 1 or higher, it is. Note: lm stands for Long Mode which equates to a 64-bit CPU.

Now see if your running kernel is 64-bit, just issue the following command:

#uname -m
x86_64 indicates a running 64-bit kernel. If you use see i386, i486, i586 or i686, you're running a 32-bit kernel.
```

```shell
<domain type='kvm'>
  <name>Ubuntu-11.04-i686_Base</name>
  <uuid>4b4c19e8-9d76-0c9dcbf8-12141823d393</uuid>
  <memory>524288</memory>
  <currentMemory>524288</currentMemory>
  <vcpu>2</vcpu>
  <os>
  <type arch='i686' machine='pc-0.14'>hvm</type>
  <boot dev='cdrom'/>
  <boot dev='hd'/>
  <bootmenu enable='no'/>
  </os>
  
  32-bit CPU  "arch='i686'"
  
  64-bit CPU  "arch='x86_64'"
  ....
  <domain/>
```

  [check before install]:https://help.ubuntu.com/community/KVM/Installation
