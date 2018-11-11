---
ID: 3157
post_title: 'MariaDB &#8211; Navigating your way around a MySQL Database'
author: sher
post_excerpt: "In this article we're going to show some simple MySQL commands to help you navigate your way around databases and tables, and also view a table's content."
layout: post
permalink: >
  https://codingbee.net/tutorials/rhce/mariadb-navigating-your-way-around-a-mysql-database
published: true
post_date: 2018-03-27 12:21:18
---
In this article we're going to show some simple MySQL commands to help you navigate your way around databases and tables, and also view a table's content. First we create a new mysql session:

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


Note: Here we ran a command as opposed to a sql query. by convention these commands are always written in upper case. Whereas sql queries can be a mix of upper and lower cases. 

Here we have 3 databases that comes as default. We can start querying one of these databases, but before we can do that we first need to select the database we're interested in:


<pre>
MariaDB [(none)]> USE mysql;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [mysql]>

</pre>

Notice that the command prompt has changed. Now we can list the tables that this database has:

<pre>
MariaDB [mysql]> SHOW TABLES;
+---------------------------+
| Tables_in_mysql           |
+---------------------------+
| columns_priv              |
| db                        |
| event                     |
| func                      |
| general_log               |
| help_category             |
| help_keyword              |
| help_relation             |
| help_topic                |
| host                      |
| ndb_binlog_index          |
| plugin                    |
| proc                      |
| procs_priv                |
| proxies_priv              |
| servers                   |
| slow_log                  |
| tables_priv               |
| time_zone                 |
| time_zone_leap_second     |
| time_zone_name            |
| time_zone_transition      |
| time_zone_transition_type |
| user                      |
+---------------------------+
24 rows in set (0.00 sec)

</pre>

Next if you want to see a list of columns that makes up the 'user' table then we do:


<pre>
MariaDB [mysql]> SHOW COLUMNS FROM user;
+------------------------+-----------------------+------+-----+---------+-------+
| Field                  | Type                  | Null | Key | Default | Extra |
+------------------------+-----------------------+------+-----+---------+-------+
| Host                   | char(60)              | NO   | PRI |         |       |
| User                   | char(16)              | NO   | PRI |         |       |
| Password               | char(41)              | NO   |     |         |       |
| Select_priv            | enum('N','Y')         | NO   |     | N       |       |
| Insert_priv            | enum('N','Y')         | NO   |     | N       |       |
| Update_priv            | enum('N','Y')         | NO   |     | N       |       |
| Delete_priv            | enum('N','Y')         | NO   |     | N       |       |
| Create_priv            | enum('N','Y')         | NO   |     | N       |       |
| Drop_priv              | enum('N','Y')         | NO   |     | N       |       |
| Reload_priv            | enum('N','Y')         | NO   |     | N       |       |
| Shutdown_priv          | enum('N','Y')         | NO   |     | N       |       |
| Process_priv           | enum('N','Y')         | NO   |     | N       |       |
| File_priv              | enum('N','Y')         | NO   |     | N       |       |
| Grant_priv             | enum('N','Y')         | NO   |     | N       |       |
| References_priv        | enum('N','Y')         | NO   |     | N       |       |
| Index_priv             | enum('N','Y')         | NO   |     | N       |       |
| Alter_priv             | enum('N','Y')         | NO   |     | N       |       |
| Show_db_priv           | enum('N','Y')         | NO   |     | N       |       |
| Super_priv             | enum('N','Y')         | NO   |     | N       |       |
| Create_tmp_table_priv  | enum('N','Y')         | NO   |     | N       |       |
| Lock_tables_priv       | enum('N','Y')         | NO   |     | N       |       |
| Execute_priv           | enum('N','Y')         | NO   |     | N       |       |
| Repl_slave_priv        | enum('N','Y')         | NO   |     | N       |       |
| Repl_client_priv       | enum('N','Y')         | NO   |     | N       |       |
| Create_view_priv       | enum('N','Y')         | NO   |     | N       |       |
| Show_view_priv         | enum('N','Y')         | NO   |     | N       |       |
| Create_routine_priv    | enum('N','Y')         | NO   |     | N       |       |
| Alter_routine_priv     | enum('N','Y')         | NO   |     | N       |       |
| Create_user_priv       | enum('N','Y')         | NO   |     | N       |       |
| Event_priv             | enum('N','Y')         | NO   |     | N       |       |
| Trigger_priv           | enum('N','Y')         | NO   |     | N       |       |
| Create_tablespace_priv | enum('N','Y')         | NO   |     | N       |       |
| ssl_type               | enum('','ANY')        | NO   |     |         |       |
| ssl_cipher             | blob                  | NO   |     | NULL    |       |
| x509_issuer            | blob                  | NO   |     | NULL    |       |
| x509_subject           | blob                  | NO   |     | NULL    |       |
| max_questions          | int(11) unsigned      | NO   |     | 0       |       |
| max_updates            | int(11) unsigned      | NO   |     | 0       |       |
| max_connections        | int(11) unsigned      | NO   |     | 0       |       |
| max_user_connections   | int(11)               | NO   |     | 0       |       |
| plugin                 | char(64)              | NO   |     |         |       |
| authentication_string  | text                  | NO   |     | NULL    |       |
+------------------------+-----------------------+------+-----+---------+-------+
42 rows in set (0.01 sec)
</pre>

Note, an alternative command we could have used is <code>DESCRIBE user;</code>. This would have shown the same output as above. 

Now we going to view contents of a table. Here's how to view the Host, User, and Password fields of the user table, we do:


<pre>
MariaDB [mysql]> SELECT Host,User,Password FROM user;
+-----------+------+-------------------------------------------+
| Host      | User | Password                                  |
+-----------+------+-------------------------------------------+
| localhost | root | *B638EC5422004FCF44EE84FABA603D29A2259BC0 |
| 127.0.0.1 | root | *B638EC5422004FCF44EE84FABA603D29A2259BC0 |
| ::1       | root | *B638EC5422004FCF44EE84FABA603D29A2259BC0 |
| 10.2.5.12 | root | *B638EC5422004FCF44EE84FABA603D29A2259BC0 |
+-----------+------+-------------------------------------------+
4 rows in set (0.00 sec)

MariaDB [mysql]>
</pre> 

If we wanted to view the entire table then we would do:


<pre>
MariaDB [mysql]> SELECT * FROM user;
</pre>


[post-content post_name=rhsca-quiz] 

[accordion]
[toggle title="What is the mysql query to get a list of all column names for the table 'user'?"]
SHOW COLUMNS FROM user;
# or 
DESCRIBE user;
[/toggle]
[toggle title="What is the mysql command to show the Host, User, and Password columns from the table 'user'?"]
SELECT Host,User,Password FROM user;
[/toggle]
[toggle title="What is the mysql command to show all columns for the table 'user'?"]
SELECT * FROM user;
[/toggle]
[/accordion]