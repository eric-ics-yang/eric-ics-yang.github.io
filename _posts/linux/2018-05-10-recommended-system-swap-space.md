### Recommended System Swap Space

Table 15.1. Recommended System [swap space]

|Amount of RAM in the system|	Recommended swap space|	Recommended swap space if allowing for hibernation|
|-------|-------|------|
|⩽ 2 GB|	2 times the amount of RAM	|3 times the amount of RAM|
|> 2 GB – 8 GB|	Equal to the amount of RAM|	2 times the amount of RAM|
|> 8 GB – 64 GB|	At least 4 GB|	1.5 times the amount of RAM|
|> 64 GB|	At least 4 GB|	Hibernation not recommended|


If your system resources allow for it, increasing the swap space may lead to better performance. 

A swap space of at least 100 GB is recommended for systems with over 140 logical processors or over 3 TB of RAM.


Note that distributing swap space over multiple storage devices also improves swap space performance, particularly on systems with fast drives, controllers, and interfaces.


Procedure 15.1. Extending Swap on an LVM2 Logical Volume
```
- Disable swapping for the associated logical volume:
# swapoff -v /dev/VolGroup00/LogVol01
- Resize the LVM2 logical volume by 2 GB:
# lvresize /dev/VolGroup00/LogVol01 -L +2G
- Format the new swap space:
# mkswap /dev/VolGroup00/LogVol01
- Enable the extended logical volume:
# swapon -v /dev/VolGroup00/LogVol01
To test if the logical volume was successfully extended, use cat /proc/swaps or free to inspect the swap space.
```

  [swap space]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/ch-swapspace
