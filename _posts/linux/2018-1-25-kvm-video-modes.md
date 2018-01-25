### cirrus

A KVM running on CentOS using libivrt, will has a video model cirrus by default.
- kernel 3.10.0-514.36.5
- libvirt 3.3.0
- qemu 2.8.1.1
```
<video>
  <model type="cirrus" vram='16384' heads='1'/>
  <address type='PCI' domain="0x0000" bus="0x00" slot="0x02" function="0x0"
</video>
```

After some OS is installed, there is something abnormal happened, for example deepin server 15.1 will not show login screen(blurred  screen )
. Ubuntu 14.04 will  have problem on gnome-terminal.

### qxl

```
<video>
  <model type="qxl" vram='65536' heads='1' ram="65536" vgamem="16384"/>
  <address type='PCI' domain="0x0000" bus="0x00" slot="0x02" function="0x0"
</video>
```

### Ubuntu 12.04 server halted when boot

It halted after "fsck from util-linux 2.20.1".

May also halted after some startup process after fsck.

```
#grep ERROR /var/log/syslog
Jane 16 09：11：31 ubuntu kernel :[2.235115] [drm:cirrus_vram_init] *ERROR* can't reserve VRAM
```

So the problem  is video! We need to change different video model.
