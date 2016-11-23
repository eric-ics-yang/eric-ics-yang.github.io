###查询多个软件包的信息

批量查询'AAA','BBB' rpm软件包的信息。

    echo 'AAA BBB' | xargs rpm -qi
    
###将参数分行分列

    echo 'AA BB CC DD' | xargs -n1
    AA
    BB
    CC
    DD
    
    echo 'AA BB CC DD' | xargs -n2
    AA BB
    CC DD

###从文件中读取参数

将文件作为输入，而不是从stdin

    xargs -a fromfile  ls
    
###重命名文件

批量重命名文件或命令需要两个依赖输入操作数的参数

    echo 'free' | xargs -t -i mv {} {}.bak   #旧命令格式
    mv free free.bak
    
    echo 'free' | xargs -t -I '{}' mv {} {}.bak  #新的格式
    mv free free.bak
    
    -t会把执行的命令打印出来 等效 --verbose
