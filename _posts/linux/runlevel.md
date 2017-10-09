### Runlevel

Runlevel 5 will start X Windows, to close X Windows, on CentOS 6 you should edit /etc/inittab.

```sh
$cat /etc/inittab
# Default runlevel. The runlevels used are:
#   0 - halt (Do NOT set initdefault to this)
#   1 - Single user mode
#   2 - Multiuser, without NFS (The same as 3, if you do not have networking)
#   3 - Full multiuser mode
#   4 - unused
#   5 - X11
#   6 - reboot (Do NOT set initdefault to this)
# 
id:3:initdefault: 

$init 3 //will close X Windows, turn to runlevel 3
$startx //will start X Windows

CentOS 7 inittab is someting like:
# multi-user.target: analogous to runlevel 3
# graphical.target: analogous to runlevel 5

it is controled by systemctl .
```
