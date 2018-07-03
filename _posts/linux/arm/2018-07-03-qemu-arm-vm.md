### Some blogs about run a ARM VM using qemu

- Install CentOS 7 on your favourite ARMv8 ARM64 AArch64 board:  [centos7] 
- CentOS ISO for ARM: [altArch] [altArchInfo]
- Qemu guest ARM support: [qemu]


### Qemu Aarch64 supported Boards

The "how do I choose a board" question is quite a common one and we document the usual answer on the project's wiki: 
http://wiki.qemu.org/Documentation/Platforms/ARM

The short answer for AArch64 is that you want to use the "virt" board, unless you specifically know that you want to emulate one of 

the 64-bit Xilinx boards (which it sounds like you don't). You'll also need to specify the CPU type with -cpu cortex-a53, 

since the "virt" board's default is cortex-a15 (a 32-bit CPU).

The qemu-system-aarch64 binary supports all the 32-bit CPUs and boards, in the same way that qemu-system-x86_64 lets you run a 32-bit x86 

CPU guest, which is why the list is so long and full of 32-bit boards. 

You cannot just try to use a 32-bit board with a -cpu cortex-a53, though -- this is like trying to plug a Core2Duo into an old i386 

motherboard and will not function correctly even if QEMU doesn't print an error message about the combination.

For the virt board, since this is not modelling a real piece of hardware, its details are only specified in the QEMU source code 

and in the device tree blob we pass to the guest. For a bare metal guest OS, you need to know:

- there is boot flash at address 0x0 (which you can fill using the -bios or -pflash QEMU command line option)
- the UART is a pl011 at 0x0900000
- RAM starts at 0x40000000
- All other information about which devices are present and where they are in memory should be obtained from the device tree blob, 

which can be found at the bottom of RAM, assuming you are a bare metal blob loading via -bios or -pflash. (If you said you were a 
Linux kernel loading via -kernel then we pass the DTB in the way the kernel booting ABI specifies. Bare metal images usually shouldn't use -kernel, though.)

  [centos7]: https://github.com/umiddelb/aarch64/wiki/Install-CentOS-7-on-your-favourite-ARMv8-ARM64-AArch64-board
  [altArch]: https://wiki.centos.org/Download
  [altArchInfo]: https://wiki.centos.org/SpecialInterestGroup/AltArch/AArch64
  [qemu]: https://wiki.qemu.org/Documentation/Platforms/ARM
  [aarch64]: https://stackoverflow.com/questions/45206027/qemu-aarch64-supported-boards
  [qemufails]: https://stackoverflow.com/questions/40316721/qemu-build-fails-to-run-aarch64-and-arm-images 
