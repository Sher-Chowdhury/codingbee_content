---
ID: 3144
post_title: 'MariaDB &#8211; Connect to a  MariaDB server from a remote client on CentOS/RHEL 7'
author: sher
post_excerpt: >
  Here we cover how to setup a mysql
  client to connect to a mysql server.
layout: post
permalink: >
  https://codingbee.net/tutorials/rhce/mariadb-install-and-configure-a-mariadb-server-on-centos-rhel-7-2
published: true
post_date: 2018-03-22 21:13:47
---
In the previous article we set up MariaDB server, now we are going to access it from a mysql client. In our example we'll try to connect using the root mysql account (which has the password, 'rootpassword') from a mysql client server that has the ip address of 10.2.5.12. 

You can follow along this article using our <a href="https://github.com/Sher-Chowdhury/CentOS7-MariaDB-demo">MariaDB vagrant project on Github</a>.


To do this, we first install the following on the mysql client:


<pre>
$ yum groupinstall -y mariadb
$ yum groupinstall -y mariadb-client
</pre> 


We installed the MariaDB server software too. That's to ensure we install all the necessary library files.

Next on the MariaDB server, we need to whitelist our root user to be allowed to have access from the remote client, this is done by running the following command:

<pre>
[root@mariadb-server ~]# mysql -u root -p
Enter password:
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 7
Server version: 5.5.56-MariaDB MariaDB Server

Copyright (c) 2000, 2017, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> <strong>GRANT ALL on *.* TO root@'10.2.5.12' IDENTIFIED BY 'rootpassword';</strong>
Query OK, 0 rows affected (0.00 sec)

MariaDB [(none)]> <strong>FLUSH PRIVILEGES;</strong>
Query OK, 0 rows affected (0.00 sec)

MariaDB [(none)]> quit
Bye
</pre>

Note, we have specify the password to remove possibility of ambiguity because in mysql it's possible to have multiple users with the same username. 

On the client we can then create a new mysql session like this:


<pre>
[root@mariadb-client ~]# mysql -h 10.2.5.10 -u root -p
Enter password:
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 8
Server version: 5.5.56-MariaDB MariaDB Server

Copyright (c) 2000, 2017, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]>
</pre>




<h2>Disabling remote access</h2>
If you want to lock down access, so that you can only create mysql sessions if you are logged in locally, then you can do this by inserting the following line in the <code>/etc/my.cnf</code> file, in the 'mysqld' stanza:

<pre>
skip-networking=1
</pre>

This will result in only socket based (i.e. local based) access being allowed. This setting also overrides the bind-address setting. 




[post-content post_name=rhsca-quiz] 

[accordion]
[toggle title="What are the steps to give the mysql user 'root' msyql access from mysql client (which has ip of 10.2.5.12) ?"]
- install mariadb software on the mysql client
- locally start mysql session as root user, so to add root user access rule for given client's ip address
- flush privileges
- attempt connection from the client
[/toggle]
[toggle title="What is the mysql query to give full access to the mysql user account 'root' (which has the password 'rootpassword') when this account creates new mysql session from a mysql client that has the ip address of 10.2.5.12?"]
GRANT ALL on *.* TO root@'10.2.5.12' IDENTIFIED BY 'rootpassword';
FLUSH PRIVILEGES;
[/toggle]
[toggle title="What is the command to run on the client to attempt access?"]
$ mysql -h 10.2.5.10 -u root -p
[/toggle]
[toggle title="What do you need to do to disable all remote access?"]
on the mysql server add the following line to /etc/my.cnf:

skip-networking=1

then restart the daemon:
[/toggle]
[/accordion]