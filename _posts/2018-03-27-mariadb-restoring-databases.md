---
ID: 3179
post_title: 'MariaDB &#8211; Restoring databases'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhce/mariadb-restoring-databases
published: true
post_date: 2018-03-27 16:56:40
---
In the previous article we saw how to create backups of our database. Now we're going to try restoring databases. First thing we need to do is delete our database so that we can start restoring it:


We created a backup file for online_shop, so let's start by deleting that database, this is done using the 'DROP' command:

<pre>
[root@mariadb-client ~]# mysql -u root -p'rootpassword' -h mariadb-server.example.com
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 20
Server version: 5.5.56-MariaDB MariaDB Server

Copyright (c) 2000, 2017, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| online_shop        |
| performance_schema |
+--------------------+
4 rows in set (0.00 sec)

MariaDB [(none)]> <strong>DROP DATABASE online_shop;</strong>
Query OK, 1 row affected (0.01 sec)

MariaDB [(none)]> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
+--------------------+
3 rows in set (0.01 sec)

MariaDB [(none)]> quit
Bye
</pre>


Now we restore this database by running the following command:


<pre>
$ mysql -u root -p'rootpassword' -h mariadb-server.example.com online_shop < online_shop_backup.sql
ERROR 1049 (42000): Unknown database 'online_shop'
</pre>

Notice that it didn't work. That's because a single db backup requires an existing db to restore to, so we create a blank database:


<pre>
MariaDB [(none)]> CREATE DATABASE online_shop;
Query OK, 1 row affected (0.00 sec)

MariaDB [(none)]> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| online_shop        |
| performance_schema |
+--------------------+
4 rows in set (0.00 sec)

MariaDB [(none)]> USE online_shop
Database changed
MariaDB [online_shop]> SHOW TABLES;
Empty set (0.00 sec)
</pre>

Now we have recreated the online_shop database but it's totally empty. Now we try performing the restore again:


<pre>
$ mysql -u root -p'rootpassword' -h mariadb-server.example.com online_shop < online_shop_backup.sql
$
</pre>

This time it has worked, which we can now see:

<pre>
MariaDB [(none)]> USE online_shop;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [online_shop]> SHOW TABLES;
+-----------------------+
| Tables_in_online_shop |
+-----------------------+
| customers             |
+-----------------------+
1 row in set (0.00 sec)

MariaDB [online_shop]> SELECT * FROM customers;
+--------+---------------+--------------+-------------------------------+
| userID | userFirstName | userLastName | userEmailAddress              |
+--------+---------------+--------------+-------------------------------+
|      1 | Peter         | Parker       | spiderman.gmail.com           |
|      2 | Tony          | Stark        | ironman.gmail.com             |
|      4 | Bruce         | Banner       | the_incredible_hull.gmail.com |
+--------+---------------+--------------+-------------------------------+
3 rows in set (0.00 sec)

MariaDB [online_shop]>
</pre>

If your backup file contains backups for multiple databases or is an entire backup, then you don't need to manually create any blank databases. Instead just do:


<pre>$ mysql -u root -p'rootpassword' -h mariadb-server.example.com < mysql_and_online_shop_backup.sql

$ mysql -u root -p'rootpassword' -h mariadb-server.example.com < backup_everything.sql
</pre>

In both cases you don't need to specify any database names, instead you just feed in the sql files.  



[post-content post_name=rhsca-quiz] 

[accordion]

[toggle title="What is the mysql command to drop the database called 'online_shop'?"]
DROP DATABASE online_shop;
[/toggle]

[toggle title="What is the command to restore the 'online_shop' database from a backup file called 
'online_shop_backup.sql' which happens to contain the backup of a single database?"]<pre>
#First create a blank database called 'online_shop'
CREATE DATABASE online_shop
#Then populate the database
$ mysql -u root -p'rootpassword' online_shop < online_shop_backup.sql</pre>
[/toggle]
[toggle title="What is the command to restore from a backup file that contains multiple databases, or even the whole mysql server databases?"]<pre>
$ mysql -u root -p'rootpassword' -h mariadb-server.example.com < backup_everything.sql</pre>
[/toggle]
[/accordion]