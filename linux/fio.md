###Rand Read/Write

    fio -ioengine=libaio -bs=4k -direct=1 -thread -rw=randread -size=1G -filename=/dev/vdc -name="QoS test" -iodepth=8 -numjobs=4 -group_reporting

    fio -ioengine=libaio -bs=4k -direct=1 -thread -rw=randwrite -size=1G -filename=/dev/vdc -name="QoS test" -iodepth=8 -numjobs=4 -group_reporting

    在centos7.1和7.2中分别执行下面的命令：
    fio -filename /dev/vdb  -iodepth 32 -thread -rw=read -ioengine=libaio -bs=1M -size=60G -numjobs=32  -group_reporting -name=sheep
    iostat统计设备性能结果.
