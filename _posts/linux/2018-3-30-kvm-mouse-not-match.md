### 虚拟机内部鼠标与外部不同步
```
输入设备：
      输入设备允许使用图形化界面和虚拟机交互，当有图形化framebuffer的时候，输入设备会被自动提供的。
      <input type='mouse' bus='ps2'/>
      input元素：input元素含有一个强制的属性，type属性的值可以是mouse活tablet，前者使用想对运动，后者使用绝对运动。
      bus属性指定一个明确的设备类型，值可以是：xen、ps2、usb。


  <input type=’tablet’ bus=’usb’/>

（该句位于<devices>配置中）
```

kvm vnc鼠标不同步的解决方式以及原因
然后:

virsh edit server003

把这行 <input type='mouse' bus='ps2'/> 改成  <input type=’tablet’ bus=’usb’/> 

保存
