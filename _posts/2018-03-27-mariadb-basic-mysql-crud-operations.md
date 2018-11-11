---
ID: 3150
post_title: 'MariaDB &#8211; Basic MySQL CRUD Operations'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhce/mariadb-basic-mysql-crud-operations
published: true
post_date: 2018-03-27 11:43:35
---
In the previous article we saw how to create a new database called 'online_shop' and then a table inside this new database called 'customers'. Now we're going to show how to create/read/update/delete (crud) content in a table. 


Let's see what the situation is:


<pre>
[root@mariadb-client ~]# mysql -u root -p'rootpassword' -h mariadb-server.example.com
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 8
Server version: 5.5.56-MariaDB MariaDB Server

Copyright (c) 2000, 2017, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| customers          |
| mysql              |
| online_shop        |
| performance_schema |
+--------------------+
5 rows in set (0.00 sec)

MariaDB [(none)]> USE online_shop
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [online_shop]> SHOW TABLES;
+-----------------------+
| Tables_in_online_shop |
+-----------------------+
| customers             |
+-----------------------+
1 row in set (0.01 sec)

MariaDB [online_shop]> DESCRIBE customers;
+------------------+----------+------+-----+---------+-------+
| Field            | Type     | Null | Key | Default | Extra |
+------------------+----------+------+-----+---------+-------+
| userID           | int(11)  | YES  |     | NULL    |       |
| userFirstName    | char(25) | YES  |     | NULL    |       |
| userLastName     | char(25) | YES  |     | NULL    |       |
| userEmailAddress | char(50) | YES  |     | NULL    |       |
+------------------+----------+------+-----+---------+-------+
4 rows in set (0.01 sec)

MariaDB [online_shop]> SELECT * FROM customers;
Empty set (0.00 sec)

MariaDB [online_shop]>
</pre>


Now let's add a few rows, which is done like this:


<pre>
MariaDB [online_shop]> INSERT INTO customers (userID,userFirstName,userLastName,userEmailAddress) VALUES (1,"Peter","Parker","spiderman.gmail.com");
Query OK, 1 row affected (0.01 sec)

MariaDB [online_shop]> INSERT INTO customers (userID,userFirstName,userLastName,userEmailAddress) VALUES (2,"Tony","Stark","ironman.gmail.com");
Query OK, 1 row affected (0.01 sec)

MariaDB [online_shop]> INSERT INTO customers (userID,userFirstName,userLastName,userEmailAddress) VALUES (3,"Steve","Rogers","captain_america.gmail.com");
Query OK, 1 row affected (0.01 sec)

MariaDB [online_shop]> INSERT INTO customers (userID,userFirstName,userLastName,userEmailAddress) VALUES (4,"Bruce","Banner","the_hull.gmail.com");
Query OK, 1 row affected (0.00 sec)


MariaDB [online_shop]> SELECT * FROM customers;
+--------+---------------+--------------+---------------------------+
| userID | userFirstName | userLastName | userEmailAddress          |
+--------+---------------+--------------+---------------------------+
|      1 | Peter         | Parker       | spiderman.gmail.com       |
|      2 | Tony          | Stark        | ironman.gmail.com         |
|      3 | Steve         | Rogers       | captain_america.gmail.com |
|      4 | Bruce         | Banner       | the_hull.gmail.com        |
+--------+---------------+--------------+---------------------------+
4 rows in set (0.00 sec)
</pre>


We can filter the output using the 'WHERE' clause, e.g.:

<pre>
MariaDB [online_shop]> SELECT * FROM customers WHERE userLastName='Stark';
+--------+---------------+--------------+-------------------+
| userID | userFirstName | userLastName | userEmailAddress  |
+--------+---------------+--------------+-------------------+
|      2 | Tony          | Stark        | ironman.gmail.com |
+--------+---------------+--------------+-------------------+
1 row in set (0.01 sec)
</pre>

To delete a row we do:

<pre>
MariaDB [online_shop]> DELETE FROM customers WHERE userEmailAddress='captain_america.gmail.com';
Query OK, 1 row affected (0.00 sec)

MariaDB [online_shop]> SELECT * FROM customers;
+--------+---------------+--------------+---------------------+
| userID | userFirstName | userLastName | userEmailAddress    |
+--------+---------------+--------------+---------------------+
|      1 | Peter         | Parker       | spiderman.gmail.com |
|      2 | Tony          | Stark        | ironman.gmail.com   |
|      4 | Bruce         | Banner       | the_hull.gmail.com  |
+--------+---------------+--------------+---------------------+
3 rows in set (0.00 sec)
</pre>


To update a row, e.g. change Bruce Banner's email address, we do:

<pre>
MariaDB [online_shop]> UPDATE customers SET userEmailAddress='the_incredible_hull.gmail.com' WHERE userFirstName='Bruce' AND userLastName='Banner'
    -> ;
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0

MariaDB [online_shop]> SELECT * FROM customers;
+--------+---------------+--------------+-------------------------------+
| userID | userFirstName | userLastName | userEmailAddress              |
+--------+---------------+--------------+-------------------------------+
|      1 | Peter         | Parker       | spiderman.gmail.com           |
|      2 | Tony          | Stark        | ironman.gmail.com             |
|      4 | Bruce         | Banner       | the_incredible_hull.gmail.com |
+--------+---------------+--------------+-------------------------------+
3 rows in set (0.00 sec)
</pre>

We combined 2 Where clauses to reduce ambiguity. We could have also used the UserID instead.


[post-content post_name=rhsca-quiz] 

In these question we'll assume we have a table called 'customers' which has 4 columns userID, userFirstName, userLastName, and userEmailAddress

[accordion]
[toggle title="What is the command to add new entry 1,Peter,Parker,spiderman.gmail.com?"]
 INSERT INTO customers (userID,userFirstName,userLastName,userEmailAddress) VALUES (1,"Peter","Parker","spiderman.gmail.com");
[/toggle]
[toggle title="What is the command to list all entries where userLastName is equal to 'Parker'?"]
SELECT * FROM customers WHERE userLastName='Stark';
[/toggle]
[toggle title="What is the command to change the email address to 'peter_parker.gmail.com' for all entries where userFirstname is 'Peter' and userLastName is 'Parker' ?"]
UPDATE customers SET userEmailAddress='peter_parker.gmail.com' WHERE userFirstName='Peter' AND userLastName='Parker'
[/toggle]
[toggle title="What is the command to delete all entries with the email address spiderman.gmail.com?"]
DELETE FROM customers WHERE userEmailAddress='spiderman.gmail.com';
[/toggle]
[/accordion]