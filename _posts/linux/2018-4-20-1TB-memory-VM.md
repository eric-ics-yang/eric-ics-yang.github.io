### topic about create a VM with 1TB memory

[1TB RAM support] is a bug about RHEL 7.5.

- qemu-kvm-rhev-2.9.0-16.el7.x86_64
- machine type: pc-q35-rhel7.4.0
- OVMF-20170228-5.gitc325e41585e3.el7.noarch

Specifying 1026 GB guest RAM, the problem is triggered. Q35 puts 2GB
RAM in the 32-bit address space, and 1024 GB above it. This means taht
the CMOS would have to express 0x100_0000 64KB chunks for the high
1024GB, which cannot be represented in the 24-bit (= 6-nibble) CMOS
register that OVMF reads (and upstream QEMU sets BTW).
  
Re-tested this bug with 3.10.0-693.5.2.el7.x86_64 & OVMF-20171011-3.git92d07e48907f.el7.noarch & qemu-kvm-rhev-2.10.0-10.el7.x86_64.

Key qemu command:

/usr/libexec/qemu-kvm -enable-kvm -M q35 -nodefaults -smp 384,cores=8,threads=24,sockets=2 -m 4T -name vm1 -drive file=/usr/share/OVMF/OVMF_CODE.secboot.fd,if=pflash,format=raw,unit=0,readonly=on -drive file=/usr/share/OVMF/OVMF_VARS.fd,if=pflash,format=raw,unit=1 -debugcon file:/home/test/ovmf.log -drive file=/usr/share/OVMF/UefiShell.iso,if=none,cache=none,snapshot=off,aio=native,media=cdrom,id=cdrom1 -device ahci,id=ahci0 -device ide-cd,drive=cdrom1,id=ide-cd1,bus=ahci0.1 -global isa-debugcon.iobase=0x402 -drive file=/home/rhel7.5-secureboot.qcow2,if=none,id=guest-img,format=qcow2,werror=stop,rerror=stop -device ide-hd,drive=guest-img,bus=ide.0,unit=0,id=os-disk,bootindex=1 -spice port=5931,disable-ticketing -vga qxl -monitor stdio -qmp tcp:0:6666,server,nowait -boot menu=on,reboot-timeout=8,strict=on -device pcie-root-port,bus=pcie.0,id=root.0,slot=0,io-reserve=0 -device e1000,netdev=tap0,mac=9a:6a:6b:6c:6d:50,bus=root.0 -netdev tap,id=tap0 -machine kernel_irqchip=split -device intel-iommu,intremap=on,eim=on -global mch.extended-tseg-mbytes=48 -serial unix:/tmp/console,server,nowait -vnc :1

Result:
4T size memory can be found inside guest. and guest works well.


1.For seabios. 
It will take ~2 minutes to boot. and take ~3 minutes to reboot.

version:
- host is RHEL7.4.z(3.10.0-693.5.2.el7.x86_64) 
- guest is RHEL7.5(3.10.0-799.el7.x86_64)
- qemu-kvm-rhev-2.10.0-10.el7.x86_64

qemu command:
/usr/libexec/qemu-kvm -enable-kvm -M q35 -nodefaults -smp 32,cores=4,threads=4,sockets=2 -m 4T -name vm1  -global isa-debugcon.iobase=0x402 -drive file=rhel7.5-seabios.qcow2,if=none,id=guest-img,format=qcow2,werror=stop,rerror=stop -device ide-hd,drive=guest-img,bus=ide.0,unit=0,id=os-disk -spice port=5931,disable-ticketing -vga qxl -monitor stdio -qmp tcp:0:6666,server,nowait -boot menu=on,reboot-timeout=8,strict=on -device pcie-root-port,bus=pcie.0,id=root.0,slot=0,io-reserve=0 -device e1000,netdev=tap0,mac=9a:6a:6b:6c:6d:50,bus=root.0 -netdev tap,id=tap0  -vnc :1

2. For OVMF
It will take ~17 minutes to boot guest. and take ~18 minutes to reboot guest.

Version:
host is RHEL7.4.z(3.10.0-693.5.2.el7.x86_64) 
guest is RHEL7.4(3.10.0-693.el7.x86_64)
qemu-kvm-rhev-2.10.0-10.el7.x86_64
OVMF-20171011-3.git92d07e48907f.el7.noarch

qemu command:

/usr/libexec/qemu-kvm -enable-kvm -M pc-q35-rhel7.5.0 -nodefaults -smp 32,cores=4,threads=4,sockets=2 -m 4T -name vm1 -drive file=/usr/share/OVMF/OVMF_CODE.secboot.fd,if=pflash,format=raw,unit=0,readonly=on -drive file=/usr/share/OVMF/OVMF_VARS.fd,if=pflash,format=raw,unit=1 -debugcon file:/home/test/ovmf.log -drive file=/usr/share/OVMF/UefiShell.iso,if=none,cache=none,snapshot=off,aio=native,media=cdrom,id=cdrom1 -device ahci,id=ahci0 -device ide-cd,drive=cdrom1,id=ide-cd1,bus=ahci0.1 -global isa-debugcon.iobase=0x402 -drive file=/home/rhel7.5-secureboot.qcow2,if=none,id=guest-img,format=qcow2,werror=stop,rerror=stop -device ide-hd,drive=guest-img,bus=ide.0,unit=0,id=os-disk,bootindex=1 -spice port=5931,disable-ticketing -vga qxl -monitor stdio -qmp tcp:0:6666,server,nowait -boot menu=on,reboot-timeout=8,strict=on -device pcie-root-port,bus=pcie.0,id=root.0,slot=0,io-reserve=0 -device e1000,netdev=tap0,mac=9a:6a:6b:6c:6d:50,bus=root.0 -netdev tap,id=tap0 -machine kernel_irqchip=split -device intel-iommu,intremap=on,eim=on -global mch.extended-tseg-mbytes=48 -serial unix:/tmp/console,server,nowait -vnc :1




  [1TB RAM support]:https://bugzilla.redhat.com/show_bug.cgi?id=1468526
