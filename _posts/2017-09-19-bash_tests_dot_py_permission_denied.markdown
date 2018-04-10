---
layout: post
title:  "bash xxx.py permission denied"
date:   2017-09-19 15:09:10 +0800
categories: automake
---

### bash tests/xxx.py   Permission Denied
Makefile.in 中check_TESTS 
bash tests/xxx.py   Permission Denied
原因是因为 xxx.py没有可执行权限，不是因为tests没有使用python xxx.py进行测试。


### ImportError: No module named mymodule

Makefile.am中包含了"AM_TEST_ENVIRONMENT=PYTHONPATH=$(PYTHONPATH):$(top_srcdir)/src;export PYTHONPATH"
但是在执行“dpkg-buildpackage -b -uc”时报错，从tests目录找不到src目录的module。

一种解决方法：
```
>>> import sys
>>> sys.path.append('/home/mymodule/')
>>>
```

另外，还可以通过修改PYTHONPATH环境变量：
```
$export PYTHONPATH=$PYTHONPATH:/home/mymodule/
```
可以就上述命令加入到"~/.bashrc" 来持久化。

第三种可能原因是，Makefile.am Makefile.in  Makefile被修改了，重新从git库中checkout即可。

