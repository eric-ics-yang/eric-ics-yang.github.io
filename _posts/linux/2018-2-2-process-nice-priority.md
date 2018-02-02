### nice

On Linux the value of nice is "-20~19", which is called static priority.[Linux process priority]
```
// run bash_test with nice 10.
#nice -n 10 ./bash_test 

```

### priority 

The value of priority ranges from 0 to 139, process whose priority ranges from 0 to 99 is called real-time process, 
while which ranges from 100 to 139 is normal process.

Priority can be changed dynamic by process scheduler of kernel, so is called dynamic priority.

已知nice值的范围是-20 - 19，其对应priority值的范围是100－139，对于一个默认nice值为0的进程来说，其初始priority值应该是120，
随着其不断执行，内核会观察进程的CPU消耗状态，并动态调整priority值，可调整的范围是+-5。
就是说，最终，其优先级可以被自动调整到115，最低到125。

```
#chrt -p pid_of_process 
pid 31127's current scheduling policy: SCHED_FIFO
pid 31127's current scheduling priority:10
```
### algorithm of kernel process scheduler 

- O1

O1调度算法是在Linux 2.6开始引入的，到Linux 2.6.23之后内核将调度算法替换成了CFS. 
这个调度器的名字之所以叫做O1，主要是因为其算法的时间复杂度是O1。
- CFS

Linux在2.6.23之后开始启用CFS作为对一般优先级(SCHED_OTHER)进程调度方法。在这个重新设计的调度器中，时间片，动态、静态优先级以及IO消耗，CPU消耗的概念都不再重要。
如何做到完全公平？答案跟上一篇IO调度中CFQ的思路类似：如果当前有n个进程需要调度执行，那么调度器应该再一个比较小的时间范围内，把这n个进程全都调度执行一遍，并且它们平分cpu时间，这样就可以做到所有进程的公平调度。
那么这个比较小的时间就是任意一个R状态进程被调度的最大延时时间，即：任意一个R状态进程，都一定会在这个时间范围内被调度相应。
这个时间也可以叫做调度周期，其英文名字叫做：sched_latency_ns。进程越多，每个进程在周期内被执行的时间就会被平分的越小。调度器只需要对所有进程维护一个累积占用CPU时间数，就可以衡量出每个进程目前占用的CPU时间总量是不是过大或者过小，这个数字记录在每个进程的vruntime中。所有待执行进程都以vruntime为key放到一个由红黑树组成的队列中，每次被调度执行的进程，都是这个红黑树的最左子树上的那个进程，即vruntime时间最少的进程，这样就保证了所有进程的相对公平。
  [Linux process priority]:http://www.linuxidc.com/Linux/2016-05/131244.htm
