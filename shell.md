###字符串操作
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
###Set

服务启动时，设置其runlevel

    runlevel
    runlevel=$(set -- $(runlevel); eval "echo \$$#" ) 
    echo $runlevel
    
    set -- A B C
    echo $#
    for i in $*;do echo $i;done

###getopt/getopts

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

###shell变量

1. $# 传递到脚本的参数个数
2. $* 以一个单字符串显示所有向脚本传递的参数。与位置变量不同，此选项参数可超过9个
3. $$ 脚本运行的当前进程ID号
4. $! 后台运行的最后一个进程的进程ID号
5. $@ 与$#相同，但是使用时加引号，并在引号中返回每个参数
6. $- 显示shell使用的当前选项，与set命令功能相同
7. $? 显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误。
```
$0 脚本名字
$1 位置参数 #1
$2 - $9 位置参数 #2 - #9
${10} 位置参数 #10
$# 位置参数的个数 
$_ 之前命令的最后一个参数
$! 运行在后台的最后一个作业的进程ID(PID) 
```
