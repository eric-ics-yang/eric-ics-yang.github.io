###make a increased number by line in vim

I need to create some lines which contains a sequence of increased number line by line.
- copy lines with same number, eg "1"
```sh
cat t
1
1
1
1
1
1
1
1
1
1
1
1
1
```

- In vim's command mode, input "let i=0 | g/**1**/s//\=i/ | let i=i+1" . "**1**" is the number you copied to every line.
```sh
:let i=0 | g/1/s//\=i/ | let i=i+1
// After that command, you will get number you needed
cat t
0
1
2
3
4
5
6
7
8
9
10
11
12

```
- Our target is <vcpupin vcpu='0' cpuset='0' />
Record an action, change "0" to "0 0", and repeat that action. like:
```sh
cat t 
0 0
1 1
......
12 12
```
Recoard an action, change "0 0" to "\<vcpupin vcpu='0' cpuset='0' /\>" and repeat that action.
```sh
cat t 
<vcpupin vcpu='0' cpuset='0' />
......
<vcpupin vcpu='12' cpuset='12' />
```
