---
layout: post
title:  "Get USB information"
categories: linux
---
### list usb devices

```
#lsusb
#lsusb -v
#lsusb -t
/: Bus 02.Port 1: Dev 1, Class=root\_hub, Driver=ehci_hcd/6p, 480M
|__ Port 1: Dev 4, If 0, Class=stor., Driver=usb-storage, 480M
|__ Port 6: Dev 3, If 0, Class=’bInterfaceClass 0x0e not yet handled’, Driver=uvcvideo, 480M

$ lsusb -D /dev/bus/usb/008/002

Device: ID 0a5c:217f Broadcom Corp. Bluetooth Controller
Couldn’t open device, some information will be missing
Device Descriptor:
bLength 18
bDescriptorType 1
bcdUSB 2.00
```
 bcdUSB的值为0x0200,代表USB协议的版本号,此处2.0版,如果为 0x0110则表示1.1版.

 USB driver info from [usb controller]
 1. OHCI（Open Host Controller Interface）是支持USB1.1的标准，但它不仅仅是针对USB，还支持其他的一些接口，比如它还支持Apple的火线（Firewire，IEEE 1394）接口。
 2. UHCI（Universal Host Controller Interface），是Intel主导的对USB1.0、1.1的接口标准，与OHCI不兼容。
 3. EHCI（Enhanced Host Controller Interface），是Intel主导的USB2.0的接口标准。
 4. xHCI（eXtensible Host Controller Interface），是最新最火的USB3.0的接口标准，它在速度、节能、虚拟化等方面都比前面3中有了较大的提高。

  [usb controller]: http://smilejay.com/2012/10/usb_controller_xhci/
