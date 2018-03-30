### 磁盘IO监控

```
$ iotop -oP

命令的含义：只显示有I/O行为的进程

$ pidstat -d 1

命令的含义：展示I/O统计，每秒更新一次

以上两个命令都需要内核版本支持kernels 2.6.20 and later only
```


