### numa command

Command to show numa configuration:
```sh
#numactl --hardware
available: 2 nodes (0-1)
node 0 cpus: 0 1 2 3 4 5 6 7 8 9 10 11 12 13 28 29 30 31 32 33 34 35 36 37 38 39 40 41
node 0 size: 130950 MB
node 0 free: 114699 MB
node 1 cpus: 14 15 16 17 18 19 20 21 22 23 24 25 26 27 42 43 44 45 46 47 48 49 50 51 52 53 54 55
node 1 size: 131072 MB
node 1 free: 118702 MB
node distances:
node   0   1 
  0:  10  21 
  1:  21  10 

# numastat -p 56396

Per-node process memory usage (in MBs) for PID 56396 (python)
                           Node 0          Node 1           Total
                  --------------- --------------- ---------------
Huge                         0.00            0.00            0.00
Heap                         8.50            5.00           13.49
Stack                        2.13            0.02            2.15
Private                      7.80            3.26           11.06
----------------  --------------- --------------- ---------------
Total                       18.43            8.27           26.70

#numactl --interleave=all  //to balance memory allocation across nodes in a NUMA system

#numastat 
                           node0           node1
numa_hit                21638563        26592091
numa_miss                      0               0
numa_foreign                   0               0
interleave_hit             59084           59030
local_node              21633534        26529191
other_node                  5029           62900

# lscpu 
Architecture:          x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                56
On-line CPU(s) list:   0-55
Thread(s) per core:    2
Core(s) per socket:    14
Socket(s):             2
NUMA node(s):          2
......
CPU family:            6
Model:                 79
......
Stepping:              1
CPU MHz:               1217.031
BogoMIPS:              3996.23
Virtualization:        VT-x
L1d cache:             32K
L1i cache:             32K
L2 cache:              256K
L3 cache:              35840K
NUMA node0 CPU(s):     0-13,28-41
NUMA node1 CPU(s):     14-27,42-55


```

###numa performance test

Some command to take a disk io test with different numa control.

```sh
# numactl --cpubind=0 --membind=0 dd if=/dev/zero of=./test_numa bs=1M count=1024
1024+0 records in
1024+0 records out
1073741824 bytes (1.1 GB) copied, 0.872421 s, 1.2 GB/s
# numactl --cpubind=0 --membind=1 dd if=/dev/zero of=./test_numa bs=1M count=1024
1024+0 records in
1024+0 records out
1073741824 bytes (1.1 GB) copied, 0.98946 s, 1.1 GB/s

date +%s.%N
numactl --cpubind=0 --membind=0 cp ./test_numa /dev/null
date +%s.%N

1487606911.504110764
1487606911.789415152

date +%s.%N
numactl --cpubind=0 --membind=1 cp ./test_numa /dev/null
date +%s.%N

1487606946.160958151
1487606946.424834058

```

###numa tips

Some references from web:

- NUMA架构的CPU -- 你真的用好了么？ [numa linux]
- Non-uniform Memory Access [numa wikipedia]

  [numa linux]: http://cenalulu.github.io/linux/numa/ 
  [numa wikipedia]: https://en.wikipedia.org/wiki/Non-uniform_memory_access
