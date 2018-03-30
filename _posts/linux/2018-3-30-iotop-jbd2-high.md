### jbd2 at 90% io usage

通过“iotop -oP”查看磁盘io时，发现进程的io占用不高，在1M/s左右，但是/dev/sdax的IO util在90%以上，切磁盘的IO的50%左右被jdb2占用。
[jdb2 io high] 可能的原因是文件系统的缓存模式影响。

```
1.Update the mount options to use data=writeback. 
2.mount with 'barrier=1'
```



  [jdb2 io high]:https://serverfault.com/questions/363355/io-wait-causing-so-much-slowdown-ext4-jdb2-at-99-io-during-mysql-commit
