
### Based on Header

假设添加自定义头 "test-header"，当"test-header"等于test时，转发到192.168.1.2。

请求如下:

> wget --header="test-header:test" -d 网址

``` shell
server {

    #......

    location / {
        #....

        if ($http_test_header = "test"){
　　　　　　add_header X-test-header test;
            proxy_pass http://192.168.1.2;
        }    

    }    

}

```

请求时header名使用中划线，nginx里会自动转成下划线，“if ($http_xxx”这段代码里，if与(中间必须有一个空格).

add_header是在response里添加自定义头.


### Based on Cookie

前端nginx服务器监听端口8080，需要根据cookie转发，查询的cookie的键（key）为cookieName，如果该cookie值（value）以1结尾则转发到server1，以2结尾则转发到server2。

- With map

``` shell
map $COOKIE_cookieName $group {
    ~*1$ server1;
    ~*2$ server2;
    default root;
}

upstream server1 {
    server 192.168.1.1:8080 weight=1 max_fails=1 fail_timeout=30s;
}

upstream server2 {
    server 192.168.1.2:8080 weight=1 max_fails=1 fail_timeout=30s;
}

upstream root {
    server 192.168.1.0:8080 weight=1 max_fails=1 fail_timeout=30s;
}

server {
    listen 8080;
    server_name neoremind.net;

log_format main '$remote_addr - $remote_user [$time_local] "$request" ' '$status $body_bytes_sent "$http_referer" "group=

$group"'
'"$http_user_agent" $gzip_ratio $request_time "$http_x_forwarded_for"';

access_log logs/access_log main;
error_log logs/error_log;

location / {
    proxy_pass http://$group;
    proxy_set_header X-Forwarded-For $remote_addr;
} 
}

```

- With set
``` shell
upstream server1 {
    server 192.168.1.1:8080 weight=1 max_fails=1 fail_timeout=30s;
}

upstream server2 {
    server 192.168.1.2:8080 weight=1 max_fails=1 fail_timeout=30s;
}

upstream root {
    server 192.168.1.0:8080 weight=1 max_fails=1 fail_timeout=30s;
}

server {
listen 8080;
server_name test.server.com;

#match cookie
set $group "root";
if ($http_cookie ~* "cookieName=([^;]+)(1$)"){
    set $group server1;
}
if ($http_cookie ~* "cookieName=([^;]+)(2$)"){
    set $group server2;
}

log_format main '$remote_addr - $remote_user [$time_local] "$request" '
'$status $body_bytes_sent "$http_referer" "group=$group"'
'"$http_user_agent" $gzip_ratio $request_time "$http_x_forwarded_for"';

access_log logs/access_log main;
error_log logs/error_log;

location / {
    proxy_pass http://$group;
    proxy_set_header X-Forwarded-For $remote_addr;
}

}
```
