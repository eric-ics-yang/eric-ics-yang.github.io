### Disable a interface when ubuntu boot

[solution] To do so simply add this line to the above mentioned file:
    
  [solution]: https://unix.stackexchange.com/questions/111256/how-to-permanently-disable-a-network-interface


```
#/etc/network/interfaces
iface wlan0 inet manual
```
