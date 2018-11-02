### shutdown -r  failed with communication error with "/var/run/dbus/system_bus_socket"

Steps to fix:

```shell
netstat -anp | grep /var/run/dbus/system_bus_socket | wc -l 
258
rm  /var/run/dbus/system_bus_socket

netstat -anp | grep /var/run/dbus/system_bus_socket | wc -l 
5
reboot -f 
```
