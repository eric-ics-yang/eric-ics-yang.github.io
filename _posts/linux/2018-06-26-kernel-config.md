### How to check a module is in kernel or not?

One way to see if a module is in kernel or not is use "lsmod | grep MODULE_TO_FIND". This is an effective way to check modularizes features(eg. CONFIG_VIRTIO_CONSOLE=m).

Sometimes a module is been configured to be included in kernel(eg. CONFIG_SERIAL_8250=y), one way to check a module in kernel or not is:
```
grep MODULE_TO_FIND /boot/config-xxx
eg. "grep PANIC /boot/config-3.10.0-514.el7.x86_64"
```

### config a kernel module 
```
make config
make menuconfig
make kconfig (KDE)
make gconfig (GNOME)
```

Some source file:
```
Kconfig
arch/ia64/Kconfig
drivers/platform/x86/Kconfig
include/linux/acpi.h

```
