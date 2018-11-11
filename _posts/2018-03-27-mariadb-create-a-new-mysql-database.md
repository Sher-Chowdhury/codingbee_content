---
ID: 3167
post_title: 'MariaDB &#8211; Create a new MySQL Database'
author: sher
post_excerpt: "In this article we're going to create a new database and then create a table inside the new database."
layout: post
permalink: >
  https://codingbee.net/tutorials/rhce/mariadb-create-a-new-mysql-database
published: true
post_date: 2018-03-27 13:03:02
---
In this article we're going to create a new database and then create a table inside the new database. We'll call this database 'online_shop'. First we create a new mysql session:

<pre>
[root@mariadb-client ~]# mysql -u root -p'rootpassword' -h mariadb-server.example.com 
</pre>

Then let's take a look at what dbs currently exist:

<pre>
MariaDB [(none)]> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
+--------------------+
3 rows in set (0.00 sec)
</pre>


Now here's how to create the database:


<pre>
MariaDB [(none)]> CREATE DATABASE online_shop;
Query OK, 1 row affected (0.00 sec)

MariaDB [(none)]> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| online_shop        |
| mysql              |
| performance_schema |
+--------------------+
4 rows in set (0.00 sec)

MariaDB [(none)]>
</pre>

At the moment the customers db doesnt contain any tables:


<pre>
MariaDB [none]> USE online_shop
Database changed
MariaDB [online_shop]> SHOW TABLES;
Empty set (0.00 sec)

MariaDB [online_shop]>
</pre>


So let's create a table called 'customers' with the following four columns:


<pre>
MariaDB [online_shop]> CREATE TABLE customers (userID INT, userFirstName char(25), userLastName char(25), userEmailAddress char(50));
Query OK, 0 rows affected (0.00 sec)

MariaDB [online_shop]>
</pre>


Let's confirm that this has worked:


<pre>
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
4 rows in set (0.00 sec)

</pre> 

[post-content post_name=rhsca-quiz] 

[accordion]
[toggle title="What is the mysql command to create new database called 'online_shop'?"]
CREATE DATABASE online_shop;
[/toggle]
[toggle title="What is the mysql command to create a table called 'customers' with 4 columns, userID (integer), userFirstName(25chars), userLastName(25chars), and userEmailAddress(50chars) ?"]
CREATE TABLE customers (userID INT, userFirstName char(25), userLastName char(25), userEmailAddress char(50));
[/toggle]
[toggle title="question?"]
answer
[/toggle]
[toggle title="question?"]
answer
[/toggle]
[toggle title="question?"]
answer
[/toggle]
[toggle title="question?"]
answer
[/toggle]
[toggle title="question?"]
answer
[/toggle]
[/accordion]