###在Linux启动时禁止某个内核模块加载

- 1.vim /etc/modprobe.d/blacklist.conf
>   添加： “blacklist virtio_console”

- 2.在 /etc/modprobe.conf 或 /etc/modules.conf中，加入：
>   alias virtio_console off
