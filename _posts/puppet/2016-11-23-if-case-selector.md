### if 
如果值是字符串， '' 表示 false ，其余为true

test.pp
```
if true   {
        $a='true'
}else {
        $a='false'
}
 
file {"/tmp/temp6.txt":
       content =>   "$a\n",
}
```

### case
一般用于判断给定的值（或变量），是否是case里的值，如果是，就执行其后面的命令，如果都不在case里，则执行default后的，一般default需存在，类似shell里的case。

case指定一个默认值（default）,这是在没有其它选项匹配的时候才会使用。

可以在selectors和case语句中使用正则表达式。

格式：
```
case 给定值{                                                                           
    值1: {命令}
    …
    值n: {命令}
    default: {命令}
}
```

test.pp
```
case $operatingsystem   {
        CentOS:   {$mycontent="my system is CentOS"}
        redhat:   {$mycontent="my system is redhat"}
        default:   {$mycontent="my system is unknow"}
}

file {"/tmp/temp6.txt":
      content =>   "$mycontent",
}
```

### selector
selector指定一个默认值（default）,这是在没有其它选项匹配的时候才会使用。

可以在selectors和case语句中使用正则表达式。
```
$变量= 给定值 ?{                                                                           
    值1 => "字符串",
    …
    值n => "字符串",
    default => "字符串",
}
```

```
$mycontent= $operatingsystem   ? {
    "Ubuntu" =>   "debianlike",
    "Debian" => "debianlike",
    "RedHat" =>   "redhatlike",
    "Fedora" =>   "redhatlike",
"CentOS"   => "redhatlike",
default => "unknown",
}
 
file   {"/tmp/temp6.txt":
      content => "$mycontent",
}
```
