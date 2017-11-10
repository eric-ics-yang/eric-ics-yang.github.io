---
layout: post
title:  "Tips about Windows OS"
date:   2016-12-14 16:09:10 +0800
categories: tips
---

### netsh 

Update network configuration of a Windows OS with netsh.
``` cmd
netsh interface ip show address
netsh interface ip set address eth0 static 127.0.0.2 255.0.0.0 127.0.0.254
netsh interface ip delete address eth0 addr=127.0.0.2

```

### Windows 7 always search and install driver when new device plugin

After install a Windows 7 on my laptop in office, i found usb device is not available. When i plugin a usb card, usb keyboard, usb mouse, win7 always try to find a driver for the usb device, but after it install the driver, those usb device still stay unresponsive.
I tried to reinstall usb driver from device management, but problem cannot fix.

Solution from [1] solved this problem.

I should update driver from "C:/Windows/System32/DriveStore/FileRepository/"!

  [1]: https://zhidao.baidu.com/question/2201634654402367228.html
