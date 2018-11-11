---
ID: 290
post_title: 'PostgreSQL &#8211; Install PostgreSQL, and then create a DB and User Account'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/postgresql/postgresql-install-postgresql-and-then-create-a-db-and-user-account
published: true
post_date: 2015-02-04 00:00:00
---
<h2>Introduction</h2>

This guide gives you a crash course on:

<ol>
	<li>Installing the PostgreSQL software on your server</li>
	<li>Create your very first PostgreSQL DB - in our case we are going to create a db called "reviewdb"</li>
	<li>Create a new DB user account and give it full access to the new db</li>
	<li>Connect to the new db using the new db user account</li>
	<li>Remotely connect to the new db using the new db user account</li>
	<li>Making remote connections more secure</li> 
</ol>

 
<h2>Installing the PostgreSQL software on your server</h2>

First download the rpm, using curl/wget. First decide on which <a href="http://yum.postgresql.org/repopackages.php">postgresql rpm package</a> you want. e.g.: 

<pre>
curl http://yum.postgresql.org/9.3/redhat/rhel-6-x86_64/pgdg-oraclelinux93-9.3-1.noarch.rpm -o postgresql.rpm
</pre>

then, while in that directory, install it using yum:

<pre>
yum install postgresql.rpm
</pre>

Alternatively we can combine the above curl+yum commands into a single command, and run the following command instead of the above 2 commands:

<pre>yum install http://yum.postgresql.org/9.3/redhat/rhel-6-x86_64/pgdg-oraclelinux93-9.3-1.noarch.rpm </pre>

This hasn't actually installed postgres yet, it only installed the postgres yum repo, which in my case is the yum repo for postgres 9.3, which is the version I am interested in:

<pre>
[root@puppetmaster tmp]# ls -l /etc/yum.repos.d/ | grep "pgdg"
-rw-r--r--  1 root root  442 Apr 21  2014 pgdg-93-oraclelinux.repo
</pre>

In my case, the I want to install postresql v9.3, so that's what I'll search for that. 

<pre>
[root@puppetmaster tmp]# yum search "postgresql93"
Loaded plugins: refresh-packagekit
========================================================================================== N/S Matched: postgresql93 ==========================================================================================
postgresql93-debuginfo.x86_64 : Debug information for package postgresql93
postgresql93-jdbc-debuginfo.x86_64 : Debug information for package postgresql93-jdbc
postgresql93-odbc-debuginfo.x86_64 : Debug information for package postgresql93-odbc
postgresql93-python-debuginfo.x86_64 : Debug information for package postgresql93-python
postgresql93.x86_64 : PostgreSQL client programs and libraries
<strong>postgresql93-contrib.x86_64 : Contributed source and binaries distributed with PostgreSQL</strong>
postgresql93-devel.x86_64 : PostgreSQL development header files and libraries
postgresql93-docs.x86_64 : Extra documentation for PostgreSQL
postgresql93-jdbc.x86_64 : JDBC driver for PostgreSQL
postgresql93-libs.x86_64 : The shared libraries required for any PostgreSQL clients
postgresql93-odbc.x86_64 : PostgreSQL ODBC driver
postgresql93-plperl.x86_64 : The Perl procedural language for PostgreSQL
postgresql93-plpython.x86_64 : The Python procedural language for PostgreSQL
postgresql93-pltcl.x86_64 : The Tcl procedural language for PostgreSQL
postgresql93-python.x86_64 : Development module for Python code to access a PostgreSQL DB
<strong>postgresql93-server.x86_64 : The programs needed to create and run a PostgreSQL server</strong>
postgresql93-test.x86_64 : The test suite distributed with PostgreSQL

  Name and summary matches only, use "search all" for everything.
[root@puppetmaster tmp]#

</pre>

The two rpms we have shown in bold above are the ones we need. Also there is another one listed above which is called "postgresql93.x86_64", this installs the client side only which is useful for testing whether you can connect to your postgresql db from another machine. However the above two will also install the client side utility as well. 


Hence we go ahead and install them:

<pre>[root@puppetagent01 ~]# yum install postgresql93-server postgresql93-contrib
pgdg93                                                                         | 3.7 kB     00:00
pgdg93/primary_db                                                              | 147 kB     00:01
Setting up Install Process
Resolving Dependencies
--> Running transaction check
---> Package postgresql93-contrib.x86_64 0:9.3.6-1PGDG.rhel6 will be installed
--> Processing Dependency: postgresql93 = 9.3.6 for package: postgresql93-contrib-9.3.6-1PGDG.rhel6.x86_64
--> Processing Dependency: libpq.so.5()(64bit) for package: postgresql93-contrib-9.3.6-1PGDG.rhel6.x86_64
--> Processing Dependency: libossp-uuid.so.16()(64bit) for package: postgresql93-contrib-9.3.6-1PGDG.rhel6.x86_64
---> Package postgresql93-server.x86_64 0:9.3.6-1PGDG.rhel6 will be installed
--> Running transaction check
---> Package postgresql93.x86_64 0:9.3.6-1PGDG.rhel6 will be installed
---> Package postgresql93-libs.x86_64 0:9.3.6-1PGDG.rhel6 will be installed
---> Package uuid.x86_64 0:1.6.1-10.el6 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

======================================================================================================
 Package                     Arch          Version                     Repository                Size
======================================================================================================
Installing:
 postgresql93-contrib        x86_64        9.3.6-1PGDG.rhel6           pgdg93                   486 k
 postgresql93-server         x86_64        9.3.6-1PGDG.rhel6           pgdg93                   4.1 M
Installing for dependencies:
 postgresql93                x86_64        9.3.6-1PGDG.rhel6           pgdg93                   1.0 M
 postgresql93-libs           x86_64        9.3.6-1PGDG.rhel6           pgdg93                   191 k
 uuid                        x86_64        1.6.1-10.el6                public_ol6_latest         53 k

Transaction Summary
======================================================================================================
Install       5 Package(s)

Total download size: 5.8 M
Installed size: 23 M
Is this ok [y/N]: y
Downloading Packages:
(1/5): postgresql93-9.3.6-1PGDG.rhel6.x86_64.rpm                               | 1.0 MB     00:03
(2/5): postgresql93-contrib-9.3.6-1PGDG.rhel6.x86_64.rpm                       | 486 kB     00:00
(3/5): postgresql93-libs-9.3.6-1PGDG.rhel6.x86_64.rpm                          | 191 kB     00:00
(4/5): postgresql93-server-9.3.6-1PGDG.rhel6.x86_64.rpm                        | 4.1 MB     00:05
(5/5): uuid-1.6.1-10.el6.x86_64.rpm                                            |  53 kB     00:00
------------------------------------------------------------------------------------------------------
Total                                                                 348 kB/s | 5.8 MB     00:17
Running rpm_check_debug
Running Transaction Test
Transaction Test Succeeded
Running Transaction
  Installing : postgresql93-libs-9.3.6-1PGDG.rhel6.x86_64                                         1/5
  Installing : postgresql93-9.3.6-1PGDG.rhel6.x86_64                                              2/5
  Installing : uuid-1.6.1-10.el6.x86_64                                                           3/5
  Installing : postgresql93-contrib-9.3.6-1PGDG.rhel6.x86_64                                      4/5
  Installing : postgresql93-server-9.3.6-1PGDG.rhel6.x86_64                                       5/5
  Verifying  : uuid-1.6.1-10.el6.x86_64                                                           1/5
  Verifying  : postgresql93-9.3.6-1PGDG.rhel6.x86_64                                              2/5
  Verifying  : postgresql93-server-9.3.6-1PGDG.rhel6.x86_64                                       3/5
  Verifying  : postgresql93-libs-9.3.6-1PGDG.rhel6.x86_64                                         4/5
  Verifying  : postgresql93-contrib-9.3.6-1PGDG.rhel6.x86_64                                      5/5

Installed:
  postgresql93-contrib.x86_64 0:9.3.6-1PGDG.rhel6    postgresql93-server.x86_64 0:9.3.6-1PGDG.rhel6

Dependency Installed:
  postgresql93.x86_64 0:9.3.6-1PGDG.rhel6         postgresql93-libs.x86_64 0:9.3.6-1PGDG.rhel6
  uuid.x86_64 0:1.6.1-10.el6

Complete!

</pre>

Here's a quick check that postgresql's command line client at least has been installed successfully:


<pre>
[root@puppetagent01 ~]# psql --version
psql (PostgreSQL) 9.3.6
</pre>




Now do:

<pre>
[root@puppetmaster tmp]#  service postgresql-9.3 status
postgresql-9.3 is stopped
[root@puppetmaster tmp]#  service postgresql-9.3 initdb       # this starts the db
Initializing database:                                     [  OK  ]
[root@puppetmaster tmp]# chkconfig postgresql-9.3 on          # this starts this service at boot-time
[root@puppetmaster tmp]#
</pre>

Next, you start the postgres service:

<pre>
[root@puppetmaster tmp]# service postgresql-9.3 start
Starting postgresql-9.3 service:                           [  OK  ]

</pre>


The above installation, has made a bunch of <a href="http://www.postgresql.org/docs/9.3/static/reference-client.html">psql commandline utilities</a> available.


After that you need to <a href="http://stackoverflow.com/questions/11919391/postgresql-error-fatal-role-username-does-not-exist">su to the postgres user</a>:

The "postgres" account has been automatically created during the Postgres installation. Not only is the "postgres" account a Linux account, but it is also a db-user account, which is also automatically created during the postgres installation. In fact the "postgres" user account is Postgres's equivalent of the linux's "root".      


<pre>
[root@puppetmaster tmp]# sudo -u postgres -i
-bash-4.1$ psql                                # then enter the postgresql command prompt. 
psql (9.3.5)
Type "help" for help.

postgres=#

</pre>
Notice here that there is link between the postgres linux account and the postgres db account. Which is that if you run "psql" under the postgres linux user, then postgres, automatically assumes you want to connect using the corresponding db user account. 

Notice here that we used the <a href="http://www.postgresql.org/docs/9.3/static/app-psql.html">psql command-line utility</a>. 



<h2>Create your very first PostgreSQL DB</h2>
Before we create our first db, Let's first <a href="http://dba.stackexchange.com/questions/1285/how-do-i-list-all-databases-and-tables-using-psql">view a list of all PostgreSQL databases</a> that Postgres comes with out of the box, using psql's "\l" command.:

<pre>
postgres=# \l
                                  List of databases
   Name    |  Owner   | Encoding |   Collate   |    Ctype    |   Access privileges
-----------+----------+----------+-------------+-------------+-----------------------
 postgres  | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |
 template0 | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
           |          |          |             |             | postgres=CTc/postgres
 template1 | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
           |          |          |             |             | postgres=CTc/postgres
(3 rows)

</pre>



Now we <a href="http://www.postgresql.org/docs/9.3/static/tutorial-createdb.html">create our own PostgreSQL DB</a>. To do this you need to do it from the bash-prompt (while still logged in as the postgres user), and not the psql prompt:

<pre>
-bash-4.1$ createdb reviewdb
</pre>
In our case, we created a db called "reviewdb"

Note if you want to, you can also <a href="http://www.postgresql.org/docs/8.2/static/sql-dropdatabase.html">delete a postgres db</a>. 


Let's now confirm that it has been created:

<pre>
-bash-4.1$ psql
psql (9.3.5)
Type "help" for help.

postgres=# \l
                                  List of databases
   Name    |  Owner   | Encoding |   Collate   |    Ctype    |   Access privileges
-----------+----------+----------+-------------+-------------+-----------------------
 postgres  | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |
 reviewdb  | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |
 template0 | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
           |          |          |             |             | postgres=CTc/postgres
 template1 | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
           |          |          |             |             | postgres=CTc/postgres
(4 rows)

postgres=#

</pre>





<h2>Create a PostgreSQL user account</h2>

Before we create a new db user account, we should first check what db user account currently exists. There are 2 ways that you can check this, either run a sql query, or use the "\du" option:

<pre>
[root@puppetagent01 ~]# su - postgres
-bash-4.1$ psql
psql (9.3.6)
Type "help" for help.

postgres=# SELECT rolname FROM pg_roles;
 rolname
----------
 postgres
 gerrit
(2 rows)

postgres=# \du
                                List of roles
 Role name    |                   Attributes                   | Member of
--------------+------------------------------------------------+-----------
 gerritdbuser |                                                | {}
 postgres     | Superuser, Create role, Create DB, Replication | {}

postgres=#

</pre>

Note, you can find more info of the "\du" option like this:


<pre>
-bash-4.1$ psql
psql (9.3.6)
Type "help" for help.

postgres=# help
You are using psql, the command-line interface to PostgreSQL.
Type:  \copyright for distribution terms
       \h for help with SQL commands
       \? for help with psql commands
       \g or terminate with semicolon to execute query
       \q to quit
postgres=# \?
General
  \copyright             show PostgreSQL usage and distribution terms
  \g [FILE] or ;         execute query (and send results to file or |pipe)
  \gset [PREFIX]         execute query and store results in psql variables
  \h [NAME]              help on syntax of SQL commands, * for all commands
  \q                     quit psql
  \watch [SEC]           execute query every SEC seconds
.
.
.
  \du[+]  [PATTERN]      list roles
.
.
.
</pre>

In our case, we created a user called "gerritdbuser" and set the password "admin123" and we have granted full access priveleges to the "reviewdb" db. 


Now let's create a new user (which in our case will have the username "gerritdbuser") To do this we need to connect to one of postgres's pre-existing db, called "template1", in order to do this:


<pre>
-bash-4.1$ id
uid=26(postgres) gid=26(postgres) groups=26(postgres)
-bash-4.1$ psql template1
psql (9.3.5)
Type "help" for help.

template1=# CREATE USER gerritdbuser WITH PASSWORD 'admin123';
CREATE ROLE
template1=# GRANT ALL PRIVILEGES ON DATABASE "reviewdb" to gerritdbuser;
GRANT
template1=#
</pre>

Now let's check that the gerritdbuser account now exists:


<pre>
This is a placeholder. 
</pre>



Next we need to assign full control to the reviewdb db that we have created. We will do this in the next section. 


<h2>Grant full DB access privelege to the new user account</h2>

This is done like this:

<pre>
-bash-4.1$ id
uid=26(postgres) gid=26(postgres) groups=26(postgres)
-bash-4.1$ psql template1
psql (9.3.5)
Type "help" for help.

template1=# GRANT ALL PRIVILEGES ON DATABASE "reviewdb" to gerritdbuser;
GRANT
template1=#
</pre>


Now we can check that this has been successful by viewing the last column of the "\l" command:


<pre>
This is a placeholder. 
</pre>





<h2>Connect to the new db with the new db user account</h2>
At this point you will have realized that when you run psql on it's own, then by default, psql will assume that you want to connect to the db with the same db-account-name as the linux account name. This means that psql will fail if you try to run psql under a linux account that doesn't have an equivalent db account with a matching name. For example we haven't created a db account called "root" hence    

  



At this point you should now be able to run the following command as postgress/root user:


<pre>
-bash-4.1$ psql -h 127.0.0.1 -U gerritdbuser -d reviewdb
psql: FATAL:  Ident authentication failed for user "gerritdbuser"
</pre>

To fix this we need to edit the following file:



<pre>[root@puppetmaster data]# ls /var/lib/pgsql/9.3/data/pg_hba.conf
/var/lib/pgsql/9.3/data/pg_hba.conf</pre>


In this file, you will find the following line:

<pre>
host    all             all             127.0.0.1/32            ident

</pre>

Here you simply replace "ident" with "trust", and the restart the postgres service.


Now if you repeat the psql call, you get:

<pre>
[root@puppetagent01 ~]# psql -h 127.0.0.1 -U gerritdbuser -d reviewdb
psql (9.3.6)
Type "help" for help.

reviewdb=>
</pre>

Success!



<h2>
Connecting to db server remotely
</h2>


Now let's try connection using the machine ip address instead:


<pre>
[root@puppetagent01 ~]# psql -h 10.1.172.11 -U gerritdbuser -d reviewdb
psql: could not connect to server: Connection refused
        Is the server running on host "10.1.172.11" and accepting
        TCP/IP connections on port 5432?
[root@puppetagent01 ~]# 
</pre>

To fix this, you need to first edit the following file:

<pre>
[root@puppetagent01 ~]# vi /var/lib/pgsql/9.3/data/postgresql.conf

</pre>

On this file, we have the listen_addresses setting:


<pre>
.
.
#------------------------------------------------------------------------------
# CONNECTIONS AND AUTHENTICATION
#------------------------------------------------------------------------------

# - Connection Settings -

#listen_addresses = 'localhost'    # what IP address(es) to listen on;
.
.
</pre>

uncomment this line and set this to: '*', i.e.:


<pre>
.
.
#------------------------------------------------------------------------------
# CONNECTIONS AND AUTHENTICATION
#------------------------------------------------------------------------------

# - Connection Settings -

#listen_addresses = 'localhost'    # what IP address(es) to listen on;
listen_addresses = '*'    # what IP address(es) to listen on;
.
.
</pre>

Note: in my case I duplicated this line before editing it. 

Then restart the postgres service and try again. You should now get the following error message:


<pre>
[root@puppetagent01 ~]# psql -h 10.1.172.11 -U gerritdbuser -d reviewdb
psql: FATAL:  no pg_hba.conf entry for host "10.1.172.11", user "gerritdbuser", database "reviewdb", SSL off
</pre>

Now let's edit our pg_hba.conf and add in an entry for our ip address, which in this case is "10.1.172.11". Hence we add the following line:

<pre>
host    all             all             10.1.172.11/32            trust
</pre>

Note: this is nearly the same as our earlier 127.0.0.1 entry. 

Now restart the postgres service and try again:

<pre>
[root@puppetagent01 ~]# psql -h 10.1.172.11 -U gerritdbuser -d reviewdb
psql (9.3.6)
Type "help" for help.

reviewdb=> \q
[root@puppetagent01 ~]#

</pre>


Success!!!



Also see: http://dba.stackexchange.com/questions/14740/how-to-use-psql-with-no-password-prompt for better security. 
=======================================================================


On the client-machine run:

<pre>

yum install http://yum.postgresql.org/9.3/redhat/rhel-6-x86_64/pgdg-oraclelinux93-9.3-1.noarch.rpm 

</pre>

Next, we just want to install the postgres client software rather than the whole postgres db software:

<pre>
[root@puppetagent03 ~]# yum search "postgresql93" | grep "^postgres" | grep "client"
postgresql93.x86_64 : PostgreSQL client programs and libraries
[root@puppetagent03 ~]#
</pre>


So let's go ahead and do this now:

<pre>
[root@puppetagent03 ~]# psql --version
-bash: psql: command not found
[root@puppetagent03 ~]# yum install postgresql93
Setting up Install Process
Resolving Dependencies
--> Running transaction check
---> Package postgresql93.x86_64 0:9.3.6-1PGDG.rhel6 will be installed
--> Processing Dependency: postgresql93-libs = 9.3.6-1PGDG.rhel6 for package: postgresql93-9.3.6-1PGDG.rhel6.x86_64
--> Processing Dependency: libpq.so.5()(64bit) for package: postgresql93-9.3.6-1PGDG.rhel6.x86_64
--> Running transaction check
---> Package postgresql93-libs.x86_64 0:9.3.6-1PGDG.rhel6 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

===============================================================================================================================================================================================================
 Package                                                Arch                                        Version                                                  Repository                                   Size
===============================================================================================================================================================================================================
Installing:
 postgresql93                                           x86_64                                      9.3.6-1PGDG.rhel6                                        pgdg93                                      1.0 M
Installing for dependencies:
 postgresql93-libs                                      x86_64                                      9.3.6-1PGDG.rhel6                                        pgdg93                                      191 k

Transaction Summary
===============================================================================================================================================================================================================
Install       2 Package(s)

Total download size: 1.2 M
Installed size: 5.8 M
Is this ok [y/N]: y
Downloading Packages:
(1/2): postgresql93-9.3.6-1PGDG.rhel6.x86_64.rpm                                                                                                                                        | 1.0 MB     00:01
(2/2): postgresql93-libs-9.3.6-1PGDG.rhel6.x86_64.rpm                                                                                                                                   | 191 kB     00:00
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Total                                                                                                                                                                          163 kB/s | 1.2 MB     00:07
Running rpm_check_debug
Running Transaction Test
Transaction Test Succeeded
Running Transaction
  Installing : postgresql93-libs-9.3.6-1PGDG.rhel6.x86_64                                                                                                                                                  1/2
  Installing : postgresql93-9.3.6-1PGDG.rhel6.x86_64                                                                                                                                                       2/2
  Verifying  : postgresql93-libs-9.3.6-1PGDG.rhel6.x86_64                                                                                                                                                  1/2
  Verifying  : postgresql93-9.3.6-1PGDG.rhel6.x86_64                                                                                                                                                       2/2

Installed:
  postgresql93.x86_64 0:9.3.6-1PGDG.rhel6

Dependency Installed:
  postgresql93-libs.x86_64 0:9.3.6-1PGDG.rhel6

Complete!
[root@puppetagent03 ~]# psql --version
psql (PostgreSQL) 9.3.6
[root@puppetagent03 ~]#

</pre>



Now if we try to connect, we get the following error message:


<pre>
[root@puppetagent03 ~]# psql -h 10.1.172.11 -U gerritdbuser -d reviewdb
psql: FATAL:  no pg_hba.conf entry for host "10.1.172.13", user "gerritdbuser", database "reviewdb", SSL off
[root@puppetagent03 ~]# 
</pre>

This means that our client server managed to <a href="http://www.postgresql.org/docs/9.3/static/client-authentication-problems.html">successfully contact the db server, but the db server refused the connection</a>. 

To fix this we once again edit the db-server's, pg_hba.conf file. This time we insert a line representing the client server's ip address. In my case my client's ip address is "10.1.172.13", therefore insert:


<pre>
host    all             all             10.1.172.13/32            trust
</pre>

This essentially add's the client server to the whitelist. 






































Next insert the following line in the pg_hba.conf file:

<pre>
pg_hba.conf
</pre>






 
 

Now lets

- Insert the following line 

<pre>
host    all             all             {vm-ip-number}/32            trust
host    all             all             127.0.0.1/32           trust

</pre>





The next few things I think we need to do :







- Into the pg_hba.conf file:
[root@puppetmaster data]# ls /var/lib/pgsql/9.3/data/pg_hba.conf
/var/lib/pgsql/9.3/data/pg_hba.conf







- is <a href="http://www.cyberciti.biz/tips/howto-iptables-postgresql-open-port.html">open up IP tables to allow posgresql</a>:
  
<pre>
iptables -A INPUT -p tcp -s 0/0 --sport 1024:65535 -d <strong>{db-server-ip-number}</strong> --dport 5432 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -p tcp -s <strong>{db-server-ip-number}</strong> --sport 5432 -d 0/0 --dport 1024:65535 -m state --state ESTABLISHED -j ACCEPT
</pre>



Note, as soon as postgres finds a match it will stop processing this file any further. Therefore you need to ensure this file doesn't contain a match earlier, if so then comment this out. 

- restart postres service to take this change into account. 




<h2>automating postgresql installation and setup with puppet</h2>

All the above can be automated using puppet which. We have a seperate article for this. 





Note: 


http://www.cyberciti.biz/tips/howto-iptables-postgresql-open-port.html




 

Useful links:

http://www.postgresql.org/docs/9.3/static/tutorial-createdb.html
http://www.thegeekstuff.com/2009/04/15-practical-postgresql-database-adminstration-commands/

http://www.cyberciti.biz/faq/psql-fatal-ident-authentication-failed-for-user/

http://www.postgresql.org/docs/9.3/static/client-authentication.html  

http://www.cyberciti.biz/faq/postgresql-remote-access-or-connection/

http://www.postgresql.org/docs/9.3/static/auth-pg-hba-conf.html

http://www.cyberciti.biz/faq/psql-fatal-ident-authentication-failed-for-user/

http://www.postgresql.org/docs/9.3/static/client-authentication-problems.html

http://www.postgresql.org/docs/9.3/static/app-psql.html

http://www.postgresql.org/docs/9.3/static/reference-client.html (these are all the commandline utilities that comes with postgres)

https://wiki.postgresql.org/wiki/YUM_Installation

http://www.postgresql.org/download/linux/redhat/

http://yum.postgresql.org/repopackages.php 

http://www.postgresql.org/docs/9.3/static/tutorial.html