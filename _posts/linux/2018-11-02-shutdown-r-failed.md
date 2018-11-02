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

#### what-is-the-difference-between-reboot-init-6-and-shutdown-r-now
reference from [stackexchange](https://unix.stackexchange.com/questions/64280/what-is-the-difference-between-reboot-init-6-and-shutdown-r-now)

There is no difference in them. Internally they do exactly the same thing:

reboot uses the shutdown command (with the -r switch).  
The shutdown command used to kill all the running processes, unmount all the file systems and finally tells the kernel to issue the ACPI power command. The source can be found here. In older distros the reboot command was forcing the processes to exit by issuing the SIGKILL signal (still found in sources, can be invoked with -f option), in most recent distros it defaults to the more graceful and init friendly init 1 -> shutdown -r. This ensures that daemons clean up themselves before shutdown.

init 6 tells the init process to shutdown all of the spawned processes/daemons as written in the init files (in the inverse order they started) and lastly invoke the shutdown -r now command to reboot the machine. Today there is not much difference as both commands do exactly the same, and they respect the init scripts used to start services/daemons by invoking the shutdown scripts for them. Except for reboot -f -r now as stated below. There is a small explanation taken from manpages of why the reboot -f is not safe:  

  -f, --force
    Force immediate halt, power-off, reboot. Don't contact the init system.

Forgot to mention, in upcoming RHEL distributions you should use the new systemctl command to issue poweroff/reboot. As stated in the manpages of reboot and shutdown they are "a legacy command available for compatibility only." and the systemctl method will be the only one safe.


