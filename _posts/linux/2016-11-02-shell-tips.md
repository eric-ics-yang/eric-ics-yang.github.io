---
layout: post
title:  "Shell tips"
categories: linux
---

### function

```shell
check_file() {
    check_file=$1
    if [ -f $check_file ]; then
        echo "file $check_file found"
    else
        echo "$check_file is not found"
        exit 1
    fi
}

check_return(){
  if [ $? -ne 0 ]; then
    echo "something wrong happened"
    exit 1
  fi
}

checkfile "/tmp/test.log"
```

### and

```shell
if [ -n "$name" ] && [ -n "$address" ];  then
    echo "name is $name, address is $address"
else
    echo "$name or $address is empty"
fi
```

### Usage

```shell
if [ $# -ne 6 ]; then
echo -e "
Usage:
    usage.sh arg1 arg2 arg3 arg11 arg22 arg33

    eg: 'usage.sh arg1 \"\" arg3 arg11 \"\" arg33'
"
exit 1
fi
```

### for in command line
```
$ for i in [nameA nameB lurenC]; do mkdir $i; done
```


### forkbomb
```
:(){ :|:& };:
```
It's called a "[forkbomb]". :() defines a function called : with the body of :|:&, meaning "run : and also run : in the background". ; ends the function definition, and : calls your new function, which endlessly spawns new versions of itself until you either hit process limits or the system grinds to a halt. It's a command that effectively freezes any system without good process limits set. Don't try this at home.

```
FreeBSD:
sysctl debug.kdb.panic=1

Linux :
echo c > /proc/sysrq-trigger
```
 
  [forkbomb]: https://unix.stackexchange.com/questions/66197/how-to-cause-kernel-panic-with-a-single-command

### check a command exist or not 
```
system_check=$(type systemctl > /dev/null 2>&1)
if [ $? = 0 ]; then
  echo "systemctl exist "
else
  echo "systemctl not exist "
fi
```

### bash -e -x

```
#!/bin/bash
set -e # exit if exit code of command is non-zero 
set -x #debug mode

```

### if directory exist

```
#!/bin/bash
DIR='/tmp/abc'
if [ ! -e $DIR ]
then
    mkdir $DIR
fi

```

```
# cat little.txt | tr  [a-z] [A-Z]
VERSION ABC
# wc -l ten.txt
10 ten.txt

# pidof sshd
2165
# history -c  #clear history of current user 
# sosreport   # collect diagnostic and configuration information 
```

### chattr
```
# chattr +a test
# rm test
rm: cannot remove 'test': Operation not permitted 
# lsattr test
-----a----- test
```
### setfacl
```
# ls -ld /root
dr-xrwx---+ root root 4096 May 10 2018
vs
dr-xrwx---.root root 4096 May 10 2018
```

"+" means this file has been set ACL.

```
# getfacl /root
 #file: root
 #owner: root
 #group: root
 user:: r-x
 user:testuser:rwx
 group:: r-x

```
### array

```shell
arr=("1.2.3.1" "1.2.3.2")
for ip in ${arr[@]}
do
  echo $ip
  ssh test@$ip "ping www.test.test"
done
```

### sed

``` shell
#substitute  IP of neutron.conf
#transport_url = rabbit://neutron:neutron123@127.0.0.1:5672
#transport_url = rabbit://neutron:neutron123@localhost:5672
#local_ip = 127.0.0.2
neutron_ip=1.2.3.4
ovs_ip=1.2.3.5
sed 's/\@.*\:5672/\@' + ovs_ip + '\:5672/' neutron.conf
sed 's/local_ip\ \=.*/local_ip\ \=' + ovs_ip + '/' openvswitch.ini

#write string to a file using sed only
------sample.txt
#
# sample

[general]
# hello = 2018
------

writeConf = "sed -i -e '1s/.*//' -e '2,$d' -e '1ifirstLine=FIRST\\nsecondLine=SECOND' sample.txt"

```

### date

```
查看当前时间
#date +%s

查看指定时间
#date -d 2017-01-01 +%s 
1483200000
#date -d 20080101 +%s
1483200000

将时间戳转换成date
date -d @1416387827 

设置时间
date -s 'Thu Jan 11 14:35:50 CST 2018'

设置hardware clock
hwclock -w   # Set the Hardware Clock to the current System Time

查询Hardware Clock
hwclock -r

hwclock --debug # 调试读取biso时间
hwclock - query and set the hardware clock (RTC)  
  
Usage: hwclock [function] [options...]  
这里需要大家特别注意：先是功能后是选项，只用其中一个是没有用的 
  
Functions:  
  -r | --show         read hardware clock and print result  
  -s | --hctosys      set the system time from the hardware clock 
  -w | --systohc      set the hardware clock to the current system time 
       --systz        set the system time based on the current timezone 
Options:  
  -u | --utc          the hardware clock is kept in UTC #设置硬件为的UTC时间  
       --localtime    the hardware clock is kept in local time #设置硬件为本地时间 
ls -alth /etc/localtime  #查看当前时区信息
timedatectl  #查看Local time UTC RTC时间等

```
[linux比BIOS时差8小时]

  [linux比BIOS时差8小时]:https://blog.csdn.net/misiter/article/details/7767146
### 字符串操作

```
#!/bin/bash
STR=singlepair,pairA:pairAA,pairB:pairBB
for pair in ${STR//,/ };do
    left=${pair%%:*}
    right=${pair##*:}
    echo $left,$right
    if type configure_${left} >/dev/null 2>&1; then
        configure_${left} ${right}
    fi
    if [[ -z "$left" ]]; then
        RIGHT=$right
    fi
done
```

### Set

服务启动时，设置其runlevel

    runlevel
    runlevel=$(set -- $(runlevel); eval "echo \$$#" ) 
    echo $runlevel
    
    set -- A B C
    echo $#
    for i in $*;do echo $i;done

### getopt/getopts

处理命令行参数是一个相似而又复杂的事情，为此，C提供了getopt/getopt_long等函数，C++的boost提供了Options库，在shell中，处理此事的是getopts和getopt.

getopts和getopt功能相似但又不完全相同，其中getopt是独立的可执行文件，而getopts是由Bash内置的。

getopts不支持长选项



参数传递的典型用法:

    ./test.sh -a -b -c ： 短选项，各选项不需参数
    ./test.sh -abc   ： 短选项，和上一种方法的效果一样，只是将所有的选项写在一起。
    ./test.sh -a args -b -c ：短选项，其中-a需要参数，而-b -c不需参数。
    ./test.sh --a-long=args --b-long ：长选项
    
test.sh

```
#!/bin/bash

while getopts "a:bc" arg #选项后面的冒号表示该选项需要参数
do
        case $arg in
             a)
                echo "a's arg:$OPTARG" #参数存在$OPTARG中
                ;;
             b)
                echo "b"
                ;;
             c)
                echo "c"
                ;;
             ?) #当有不认识的选项的时候arg为?
            echo "unkonw argument"
        exit 1
        ;;
        esac
done
```

现在就可以使用：  ./test.sh -a arg -b -c     或   ./test.sh -a arg -bc

```
# Example input and output (from the bash prompt):
# ./parse.bash -a par1 'another arg' --c-long 'wow!*/?' -cmore -b " very long "
# Option a
# Option c, no argument
# Option c, argument `more'
# Option b, argument ` very long '
# Remaining arguments:
# --> `par1'
# --> `another arg'
# --> `wow!*/?'

# Note that we use `"$@"' to let each command-line parameter expand to a
# separate word. The quotes around `$@' are essential!
# We need TEMP as the `eval set --' would nuke the return value of getopt.

TEMP=`getopt -o ab:c:: --long a-long,b-long:,c-long:: /
     -n 'example.bash' -- "$@"`

#-o表示短选项，两个冒号表示该选项有一个可选参数，可选参数必须紧贴选项
#如-carg 而不能是-c arg
#--long表示长选项
#"$@"在上面解释过
# -n:出错时的信息
# -- ：举一个例子比较好理解：
#我们要创建一个名字为 "-f"的目录你会怎么办？
# mkdir -f #不成功，因为-f会被mkdir当作选项来解析，这时就可以使用
# mkdir -- -f 这样-f就不会被作为选项。

if [ $? != 0 ] ; then echo "Terminating..." >&2 ; exit 1 ; fi

# Note the quotes around `$TEMP': they are essential!
#set 会重新排列参数的顺序，也就是改变$1,$2...$n的值，这些值在getopt中重新排列过了
eval set -- "$TEMP"

#经过getopt的处理，下面处理具体选项。

while true ; do
        case "$1" in
                -a|--a-long) echo "Option a" ; shift ;;
                -b|--b-long) echo "Option b, argument /`$2'" ; shift 2 ;;
                -c|--c-long)
                        # c has an optional argument. As we are in quoted mode,
                        # an empty parameter will be generated if its optional
                        # argument is not found.
                        case "$2" in
                                "") echo "Option c, no argument"; shift 2 ;;
                                *) echo "Option c, argument /`$2'" ; shift 2 ;;
                        esac ;;
                --) shift ; break ;;
                *) echo "Internal error!" ; exit 1 ;;
        esac
done
echo "Remaining arguments:"
for arg do
   echo '--> '"/`$arg'" ;
done

```

比如我们使用

    ./test -a -b arg arg1 -c
可以看到,命令行中多了个arg1参数，在经过getopt和set之后，命令行会变为：

    -a -b arg -c -- arg1
$1指向-a,$2指向-b,$3指向arg,$4指向-c,$5指向--,而多出的arg1则被放到了最后。

ref: [1]

  [1]: http://blog.csdn.net/qzwujiaying/article/details/6371246

### shell变量

1. $# 传递到脚本的参数个数
2. $* 以一个单字符串显示所有向脚本传递的参数。与位置变量不同，此选项参数可超过9个
3. $$ 脚本运行的当前进程ID号
4. $! 后台运行的最后一个进程的进程ID号
5. $@ 与$#相同，但是使用时加引号，并在引号中返回每个参数
6. $- 显示shell使用的当前选项，与set命令功能相同
7. $? 显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误。
8. $0 脚本名字
9. $1 位置参数 #1
10. $2 - $9 位置参数 #2 - #9
11. $_ 之前命令的最后一个参数
