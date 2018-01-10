### MySQL commands

```
#login to localhost
mysql -uroot -p 

#backup db to file
mysqldump -uroot -p testdb > testdb.sql

#restore db from backup file
#should login mysql first, then change to db which you want to restore.
mysql -uroot -p
>use testdb
>srouce testdb.sql

```
