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


### query CPU models supported 
```
# qemu-system-x86_64 --cpu ?
x86           qemu64  QEMU Virtual CPU version 2.0.0                  
x86           phenom  AMD Phenom(tm) 9550 Quad-Core Processor         
x86         core2duo  Intel(R) Core(TM)2 Duo CPU     T7700  @ 2.40GHz 
……
```
### query machines supported  

```
# qemu-system-x86_64 --machine ?
Supported machines are:
pc-0.13              Standard PC (i440FX + PIIX, 1996)
pc-i440fx-2.0        Standard PC (i440FX + PIIX, 1996)
……
```

### dmidecode
```
type 0是BIOS信息

# dmidecode --type 0
# dmidecode 2.12
# SMBIOS entry point at 0xcbadeb98
SMBIOS 2.7 present.

Handle 0x0000, DMI type 0, 24 bytes
BIOS Information

type 1是系统信息

# dmidecode --type 1
# dmidecode 2.12
# SMBIOS entry point at 0xcbadeb98
SMBIOS 2.7 present.

Handle 0x0001, DMI type 1, 27 bytes
System Information

type 2是主板信息

# dmidecode --type 2
# dmidecode 2.12
# SMBIOS entry point at 0xcbadeb98
SMBIOS 2.7 present.

Handle 0x0002, DMI type 2, 15 bytes
Base Board Information

type 4是CPU信息

# dmidecode --type 4
# dmidecode 2.12
# SMBIOS entry point at 0xcbadeb98
SMBIOS 2.7 present.

Handle 0x0004, DMI type 4, 42 bytes
Processor Information
```

