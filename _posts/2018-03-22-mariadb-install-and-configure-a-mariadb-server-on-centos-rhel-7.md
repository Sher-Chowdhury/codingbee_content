---
ID: 3136
post_title: 'MariaDB &#8211; Install and configure a MariaDB server on CentOS/RHEL 7'
author: sher
post_excerpt: "Here we'll setup a Mysql Server using the MariaDB software. "
layout: post
permalink: >
  https://codingbee.net/tutorials/rhce/mariadb-install-and-configure-a-mariadb-server-on-centos-rhel-7
published: true
post_date: 2018-03-22 18:19:42
---
MariaDB is basically a clone of MySQL. MariaDB is created by the same people who created MySQL. There was uncertainty about what would happen to MySQL after Oracle bought. This uncertainty gave birth to MariaDB. Our aim in this article is to setup a secure MariaDB server that can accept connections from remote clients. This involves the following steps:

<ol>
	<li>Install the mariadb rpms</li>
	<li>Configure MariaDB to allow connections from a network interface</li>
	<li>Perform security hardening</li>
	<li>configure firewalld to allow mysql traffic</li>
	<li>Test the mariadb server setup</li>
</ol>

You can follow along this article using our <a href="https://github.com/Sher-Chowdhury/CentOS7-MariaDB-demo">MariaDB vagrant project on Github</a>.
  
<h2>Install the mariadb rpms</h2>

The best way to install MariaDB is via group install:


<pre>
$ yum groupinstall mariadb
$ yum groupinstall mariadb-client
</pre>



<h2>Configure MariaDB to allow connections from a network interface</h2>

By default, MariaDB doesnt allow any external mysql client's to connect to it. This can be fixed by adding one of the MariaDB's server's network interfaces ip address to MariaDB's main config file, <code>/etc/my.cnf</code>, in particular we need to add this line under the 'mysqld' stanza:

<pre>
$ cat /etc/my.cnf
[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0
# Settings user and group are ignored when systemd is used.
# If you need to run mysqld under a different user or group,
# customize your systemd unit file for mariadb according to the
# instructions in http://fedoraproject.org/wiki/Systemd
<strong>bind-address=10.2.4.10</strong>



[mysqld_safe]
log-error=/var/log/mariadb/mariadb.log
pid-file=/var/run/mariadb/mariadb.pid

#
# include all files from the config directory
#
!includedir /etc/my.cnf.d
</pre>


In our example, our network interface's ip address is '10.2.4.10'. 

We still haven't updated firewalld to allow mysql traffic. We'll do that after security harding our mysql server

<h2>Perform security hardening</h2>

Security hardening our Mysql server is simply a case of running the mysql_secure_installation utility and providing information when prompted. this utility runs a series our sql scripts against our Mariadb setup. So before we can use this utility we first need to start+enable MariaDB:

<pre>
$ systemctl start mariadb
$ systemctl enable mariadb
Created symlink from /etc/systemd/system/multi-user.target.wants/mariadb.service to /usr/lib/systemd/system/mariadb.service.
</pre>

Now we run the security utility:



<pre>
$ mysql_secure_installation

NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, we'll need the current
password for the root user.  If you've just installed MariaDB, and
you haven't set the root password yet, the password will be blank,
so you should just press enter here.

<strong>Enter current password for root (enter for none): {I don't have a password yet so left this blank}</strong>
OK, successfully used password, moving on...

Setting the root password ensures that nobody can log into the MariaDB
root user without the proper authorisation.

<strong>Set root password? [Y/n] Y</strong>
<strong>New password:</strong>
<strong>Re-enter new password:</strong>
Password updated successfully!
Reloading privilege tables..
 ... Success!


By default, a MariaDB installation has an anonymous user, allowing anyone
to log into MariaDB without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

<strong>Remove anonymous users? [Y/n] Y</strong>
 ... Success!

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

<strong>Disallow root login remotely? [Y/n] Y   # this is the default, so that we override on host-by-host basis - see next article. </strong>
 ... Success!

By default, MariaDB comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

<strong>Remove test database and access to it? [Y/n] Y</strong>
 - Dropping test database...
 ... Success!
 - Removing privileges on test database...
 ... Success!

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

<strong>Reload privilege tables now? [Y/n] Y</strong>
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!
</pre>

Now we restart the mariadb daemon:


<pre>
$ systemctl restart mariadb
</pre>

Now that our mariadb server is secure, we can now go ahead and open up the firewall:


<pre>
$ firewall-cmd --permanent --add-service=mysql
success
$ systemctl restart firewalld
</pre>


<h2>Test the MariaDB server setup</h2>

We can now test our setup by first trying to connect to our database, by running:


<pre>
$ mysql -u root -p
Enter password:
</pre>

We specified the (p)assword option to tell our mysql that we want to get a password prompt. Note, at this stage we are not connecting to a particular database, just establishing an admin session with the mysql daemon. After entering the password, we get the mariadb interactive prompt:


<pre>
[root@ntp-server ~]# mysql -u root -p
Enter password:
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 7
Server version: 5.5.56-MariaDB MariaDB Server

Copyright (c) 2000, 2017, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]>
</pre>

So now let's run some hello-world like SQL, first will view a list of databases, then select a database, then view a list of tables in that data, then view the content of a particular table.



<pre>
MariaDB [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
+--------------------+
3 rows in set (0.00 sec)

MariaDB [(none)]> use mysql;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [mysql]> show tables;
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

MariaDB [mysql]> pager less -SFX
PAGER set to 'less -SFX'

MariaDB [mysql]> SELECT * FROM user;
+-----------+------+-------------------------------------------+-------------+-------------+-------------+-------------+-------------+-----------+-------------+---------------+--------------+-----------+-
| Host      | User | Password                                  | Select_priv | Insert_priv | Update_priv | Delete_priv | Create_priv | Drop_priv | Reload_priv | Shutdown_priv | Process_priv | File_priv |
+-----------+------+-------------------------------------------+-------------+-------------+-------------+-------------+-------------+-----------+-------------+---------------+--------------+-----------+-
| localhost | root | *7E48482E01D8BB29A0D21B9DC7603190B5D69A17 | Y           | Y           | Y           | Y           | Y           | Y         | Y           | Y             | Y            | Y         |
| 127.0.0.1 | root | *7E48482E01D8BB29A0D21B9DC7603190B5D69A17 | Y           | Y           | Y           | Y           | Y           | Y         | Y           | Y             | Y            | Y         |
| ::1       | root | *7E48482E01D8BB29A0D21B9DC7603190B5D69A17 | Y           | Y           | Y           | Y           | Y           | Y         | Y           | Y             | Y            | Y         |
+-----------+------+-------------------------------------------+-------------+-------------+-------------+-------------+-------------+-----------+-------------+---------------+--------------+-----------+-

MariaDB [mysql]> quit
Bye
$

</pre>

Note, the pager command is used to format a table output in a prettier format. 



[post-content post_name=rhsca-quiz] 

[accordion]
[toggle title="What are the steps to setup a MariaDB server?"]
- Install the mariadb rpms
- Configure MariaDB to allow connections from a network interface
- Perform security hardening
- configure firewalld to allow mysql traffic
- Test the mariadb server setup
[/toggle]
[toggle title="What commands do you need to run to install the MariaDB software?"]
$ yum groupinstall mariadb
$ yum groupinstall mariadb-client
[/toggle]
[toggle title="What config file needs editing and what changes needs to be made?"]
edit the file '/etc/my.cnf'

Add the following line to the [msqld] stanza:
bind-address={mariadb-server's-ip-address}
[/toggle]
[toggle title="Why does this setting needs to be made?"]
to allow remote MySQL clients to connect to it. 
[/toggle]
[toggle title="What is the command to start+enable the mariadb service?"]
$ systemctl start mariadb
$ systemctl enable mariadb
[/toggle]
[toggle title="What is the command to run to make our mariadb setup more secure?"]
$ mysql_secure_installation
[/toggle]
[toggle title="What steps do you need to take after making it more secure?"]
$ systemctl restart mariadb
$ firewall-cmd --permanent --add-service=mysql
$ systemctl restart firewalld
[/toggle]
[toggle title="What is the command to start a admin session locally?"]
$ mysql -u root -p
# this will start a mysql terminal
[/toggle]
[toggle title="What is the mysql command to list all databases?"]
show databases;
[/toggle]
[toggle title="What is the command to drill into a database called 'customers'?"]
use customers;
[/toggle]
[toggle title="What is the command to show all tables in the drilled down database?"]
show tables;
[/toggle]
[/accordion]