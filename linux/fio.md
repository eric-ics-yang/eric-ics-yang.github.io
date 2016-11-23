###Rand Read/Write

    fio -ioengine=libaio -bs=4k -direct=1 -thread -rw=randread -size=1G -filename=/dev/vdc -name="QoS test" -iodepth=8 -numjobs=4 -group_reporting

    fio -ioengine=libaio -bs=4k -direct=1 -thread -rw=randwrite -size=1G -filename=/dev/vdc -name="QoS test" -iodepth=8 -numjobs=4 -group_reporting
