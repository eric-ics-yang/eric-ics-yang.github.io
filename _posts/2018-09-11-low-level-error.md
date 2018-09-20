### connect to mysql server
Havn't connect to a MySQL server through command line for some years, I made a low level error on it.  
I try to connect to a MySQL server which listing on port 30306 with command below :

```
mysql -utest -h127.xx.xx.xx -p 30306
```
I enter the password, but got a error: "ERROR 2003 (HY000): Cann't connect to MySQL server on 127.xx.xx.xx (111)".

After ask the man, I realized "-p" means password, if I want to use a port which is not the default port, I should use parameter "-P".

The command below is the right command.
```
mysql -utest -h127.xx.xx.xx -P 30306 -p
```
