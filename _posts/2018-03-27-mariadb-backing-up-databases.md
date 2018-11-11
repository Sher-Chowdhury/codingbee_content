---
ID: 3174
post_title: 'MariaDB &#8211; Backing up databases'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhce/mariadb-backing-up-databases
published: true
post_date: 2018-03-27 15:08:52
---
There are 3 levels of mysql database backups you can do:

<ul>
	<li>Backup the contents of a single database</li>
	<li>Backup multiple databases</li>
	<li>Backup the entire mysql server</li>
</ul>

when creating database backups, the resulting backup files are actrually really big sql files that will repopulate a database. In all cases we create database backups using the <code>mysqldump</code> utility. 

<h2>Backup the contents of a single database</h2>

This is done by running the command:

<pre>
$ mysqldump -u root -p'rootpassword' -h mariadb-server.example.com online_shop > online_shop_backup.sql
</pre>

This file will populate a database's content. It won't create the database itself.  This is handy if you want to create a clone of a production db in a dev environment. It also means that when restoring this backup file, you first need to manually create an empty database for this backup file to populate. 


<h2>Backup multiple databases</h2>
To backup multiple databases we do:


<pre>
$ mysqldump -u root -p'rootpassword' -h mariadb-server.example.com <strong>--databases</strong> online_shop mysql > multiple_databases_backup.sql
</pre>

Notice we used the '--databases' option and listed out which databases we want backed up. This time, this backup file will restore the database itself as well as it's content. 


<h2>Backup mysql as a whole</h2>

Here's the command for backing up all the databases into a single backup file:

<pre>
$ mysqldump -u root -p'rootpassword' -h mariadb-server.example.com --all-databases > backup_everything.sql
</pre>
 


[post-content post_name=rhsca-quiz] 

[accordion]
[toggle title="What are the three main types of database backups you can do?"]
- Backup the contents of a single database
- Backup multiple databases
- Backup the entire mysql server
[/toggle]
[toggle title="What is the command to backup the contents of just one db called 'online_shop'?"]
$ mysqldump -u root -p'rootpassword' online_shop > online_shop_backup.sql
[/toggle]
[toggle title="What is the command to backup the contents of 2 dbs, called 'online_shop' and 'warehouse'?"]
$ mysqldump -u root -p'rootpassword' --databases online_shop warehouse > multiple_databases_backup.sql
[/toggle]
[toggle title="What is the command to backup everything?"]
$ mysqldump -u root -p'rootpassword' --all-databases > backup_everything.sql
[/toggle]
[/accordion]