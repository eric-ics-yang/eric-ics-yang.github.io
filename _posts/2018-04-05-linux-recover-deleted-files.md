### testdisk
[testdisk] 恢复分区
[tools] 其他一些恢复工具

  [testdisk]: https://help.aliyun.com/document_detail/52046.html
  [tools]: https://blog.csdn.net/qq_20480611/article/details/50759096
### extundelete
[extundelete] in epel repo.

```
#从根分区 inode 查找
extundelete /dev/sdb1 --inode 2
#通过inode一步步找到删除的文件的路径
extundelete /dev/sda5 --inode 24904454
#指定路径，恢复删除的文件
extundelete /dev/sda5 --restore-directory /webapps/xxxx/upload

```

  [extundelete]: http://blog.51cto.com/wzlinux/2052835
