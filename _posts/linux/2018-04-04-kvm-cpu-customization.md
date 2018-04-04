### how to emulate a x86 CPU

When create a VM through virsh, you can define it's configuration through a XML file.

```
## x86_64
<os>
  <type arch="x86_64" machine='pc-i440fx-2.8'>hvm</type>
  <bootmenu enable='yes' timeout="5"/>
  <bios useserial="yes"/>
</os>

##i686
<os>
  <type arch="i686" machine='pc-i440fx-2.8'>hvm</type>
  <bootmenu enable='yes' timeout="5"/>
  <bios useserial="yes"/>
</os>

```

### how to emulate a Pentium CPU

```
<cpu mode='custom' match='exact' check='partial'>
  <model fallback='forbid'> pentium </model>
  <topology socket='1' cores='1' threads='1'/>
  <feature policy='disable' name='pni'/>
</cpu>

  <model fallback='allow'> pentium </model>
```

### create xml from command line with virsh

From [domxml].
```
#cat vm.txt
qemu-system-x86_64 -m 512 -smp 2 drive file=/data/test.img -nographic
#virsh domxml-from-native qemu-argv vm.txt > test.xml
```

  [domxml]:https://blog.csdn.net/u011165931/article/details/41823431
