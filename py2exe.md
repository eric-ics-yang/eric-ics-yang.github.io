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

###生成多个exe文件
py2exe一次能够创建多个exe文件，你需要将这些脚本文件的列表传递给console或windows的关键字参数。

###配置data_files
如果在安装脚本中用data_files可选项指定了那些额外的文件，那么py2exe能将这些文件拷贝到dist子目录中。data_files应包含一个元组(target-dir, files)列表，其中的files是这些额外的文件的列表。
示例如下：

    from distutils.core import setup
    import glob
    import py2exe 
    setup(console=["helloworld.py"],
          data_files=[("bitmaps",
                       ["bm/large.gif", "bm/small.gif"]),
                      ("fonts", glob.glob("fonts\\*.fnt"))])

说明：data_files选项将创建一个子目录dist\bitmaps，其中包含两个.gif文件；一个子目录dist\fonts，其中包含了所有的.fnt文件。
应该不是用来给exe添加图标的。
###添加图标

    setup(
    //生成有指定图标的exe
    windows = [{"script": "[源码文件名].py",
                "icon_resources": [(1, u"[图标名].ico")]
               }]
    //生成无图标exe
    windows = [{"script": "[源码文件名].py"}]
    )
