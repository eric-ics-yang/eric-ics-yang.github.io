### use qemu to run a aarch64

- run aarch64 using already build img

yum install qemu-system-aarch64

    wget http://people.linaro.org/~alex.bennee/images/aarch64-linux-3.15rc2-buildroot.img
    ./aarch64-softmmu/qemu-system-aarch64 -machine virt -cpu cortex-a57 -machine type=virt -nographic -smp 1 -m 2048 -kernel aarch64-linux-3.15rc2-buildroot.img  --append "console=ttyAMA0"

 Thanks to VIRT FS we can achieve this without too much hassle. Use the following extended QEMU command line [1]
 
    ./aarch64-softmmu/qemu-system-aarch64 -machine virt -cpu cortex-a57 -machine type=virt -nographic -smp 1 -m 2048 -kernel aarch64-linux-3.15rc2-buildroot.img --append "console=ttyAMA0" -fsdev local,id=r,path=/home/alex/lsrc/qemu/rootfs/trusty-core,security_model=none -device virtio-9p-device,fsdev=r,mount_tag=r

[1]: http://www.bennee.com/~alex/blog/2014/05/09/running-linux-in-qemus-aarch64-system-emulation-mode/ 

- build qemu from souce

get the qemu source files

    cd qemu
    ./configure --target-list=aarch64-softmmu --enable-virtfs # configure for aarch64 (virtfs only needed if you'd like to mount up a dir of the host in the guest OS)
    make # build qemu (make install is not necessary)
