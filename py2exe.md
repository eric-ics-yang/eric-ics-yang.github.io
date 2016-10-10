###About

py2exe是一个将python脚本转换成windows上的可独立执行的可执行程序(*.exe)的工具，可以在不安装Python的情况下运行。

###hello world

mysetup.py
```
from distutils.core import setup
import py2exe
setup(console=["helloworld.py"]) 
```
然后按下面的方法运行mysetup.py:

python mysetup.py py2exe

上面的命令执行后将产生一个名为dist的子目录，其中包含了helloworld.exe,python24.dll,library.zip这些文件。
如果你的helloworld.py脚本中用了已编译的C扩展模块，那么这些模块也会被拷贝在个子目录中，同样，所有的dll文件在运行时都是需要的，除了系统的dll文件。
