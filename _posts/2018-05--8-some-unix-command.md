### query CPU information 
```
# psrinfo -v
# hw | more
# hwconfig
cat /system/processor/000
```

### system administration 
```
# swconfig
# scoadmin
# ls -Wv /var/adm
/usr/include/vm/cpu.h
/usr/include/sys/cpumtr.h

在内核参数添加“clock=pit”是一个解决时间突变的方法，未验证。
mca是SCO在KVM平台崩溃的一个参数。

```
