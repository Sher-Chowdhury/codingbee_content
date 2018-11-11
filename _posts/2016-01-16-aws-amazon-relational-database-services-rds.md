---
ID: 513
post_title: 'AWS &#8211; Amazon Relational Database Services (RDS)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-amazon-relational-database-services-rds
published: true
post_date: 2016-01-16 00:00:00
---
Amazon RDS is a service that makes it easy to set up, operate, and scale a relational database. Amazon RDS supports the following database engines:
<ul>
 	<li><a href="https://aws.amazon.com/rds/aurora/">Amazon Aurora</a> - this is a fork of mysql. this means that if you are familiar with mysql, then this works exactly the same way. It is 5 times faster then mysql. This isn't free, but it is about a 10% of the price of other commercial alternatives such as oracle db.</li>
 	<li>Oracle</li>
 	<li>Microsoft SQL Server</li>
 	<li>PostgreSQL</li>
 	<li>MySQL</li>
 	<li>MariaDB.</li>
</ul>
&nbsp;
<h2>Fully Managed service</h2>
With  Amazon RDS, you get a "Fully Managed" service. A fully managed service is a service where:
<ul>
 	<li>Your DB software of choice is preinstalled and configured for you.</li>
 	<li>You are not allowed to access the operating system (e.g. via ssh) that the database is running on. Instead AWS will maintain the OS for you, e.g. installing OS security patches.</li>
 	<li>RDS will automatically take regular db backups for you - These are point in time snapshots. However you can also take manual backups via your db's cli. These automated backups are also incremental backups</li>
 	<li>RDS will automatically upgrade your db software for you, you can choose to enable either automatic minor, major, or all upgrades</li>
 	<li>The underlying instance that is provideing the service will automatically scale up/down, so that you don't have to worry about it.</li>
 	<li>It has built-in fault tolerance</li>
 	<li>It has built-in high-availability</li>
 	<li>High scalability - e.g. can add extra cpu cores, ram, or diskspace while db is running.</li>
 	<li>Automatic recovery if there is a failover</li>
 	<li>You can enable  "multi-availability-zone deployments"  with a single click</li>
 	<li>You can make use of <a href="https://aws.amazon.com/rds/details/read-replicas/" rel="nofollow">read replica</a>. This makes a copy of the main db, that is kept in sync with the main db (but not in real-time), and it's main purpose is to respond to read related queries. This eases the burden on the main db. Read replica is available for:
<ul>
 	<li>mysql</li>
 	<li>postgres</li>
 	<li>aurora</li>
</ul>
</li>
</ul>
&nbsp;

&nbsp;
<h2>Multi-Availability-Zone deployments</h2>
Multi-AZ is a Disaster recovery feature. I.e. if a DB fails, then there is another DB in standby mode that can aws will automatically fail over to. Although there are side benefits of Multi-Az too, e.g. perform db upgrades with zero downtime.

In a <a href="https://aws.amazon.com/rds/details/multi-az/" rel="nofollow">RDS Multi-AZ deployment</a> setup  you have 2 databases, the first db is a the active db that acts as your application's main db. The second db is in a different availability zone, and acts as a passive db, aka standby db. The passive db is kept in sync with the active db in real-time, which is made possible because each db (sql) transtaction is applied to both active and passive db simultanuously. This setup provides the following benefits
<ul>
 	<li>You can change your disk types with no downtime</li>
 	<li>you can change your rds's instance type with no downtime</li>
 	<li>This feature is really important for building highly available, fault tolerant systems - bacause the passive db acts as a failover db, which becomes active if the main db fails for any reason.</li>
 	<li>We can initiate a manual failover if needed for any reason</li>
 	<li>backups are taken agains the passive db, so that active db's performance is not affected and there is no downtime.</li>
 	<li>Do OS updates, or db software updates with no downtime - this happens in the following setups
<ul>
 	<li>1. failover to passive db</li>
 	<li>2. Take primary db offline, perform os/db-software updates to primary instance</li>
 	<li>3. replicate passive db's data back to primary db</li>
 	<li>4. switch back to primary db</li>
 	<li>5. Take passive db offline, and  perform os/db-software updates to primary instance</li>
 	<li>6. Get passive db to sync up with primary db again</li>
</ul>
</li>
</ul>
Here are a few thinkgs to remember about this setup:
<ul>
 	<li>The primary db and it's passive db(s) are in the same region (to minimise latency), but in different availability zones (to maximize fault-tolerance/high-availability).</li>
 	<li>You don't have to worry about managing your passive dbs, aws will automatically manage all this for you behind the scenes.</li>
 	<li>If there is a main db failure, then aws straight away updates   the db's "cname" to point to the passive db's endpoint. Hence this appears seamless and downtime for the end user.</li>
</ul>
&nbsp;
<h2>RDS Underlying instance</h2>
When creating an rds:
<ul>
 	<li>you have a large choice of intance types to choose from</li>
 	<li>choose a disk size ranging from 5GB to 3TB</li>
 	<li>Can choose disk type, (burstable SSD) or Provisioned IOPS (EBS)</li>
</ul>
&nbsp;
<h2>RDS backups</h2>
There are 2 types of RDS backups:
<ul>
 	<li>Automated backups</li>
 	<li>Database Snapshots</li>
</ul>
Whether you are using <em>RDS multi-AZ deployment</em> or not, db's backup are still made.

In both cases, When you restore (aka create) a db from an existing backup, then the new db ends up with a new endpoint.

&nbsp;
<h3>Automated Backups</h3>
These are performed daily by AWS automatically. You can specify a retention period for this time of backup, which can range from 1 day to 35 days. If you set the retention period to 35 days, then it will let you 'rewind' your db to any point in time within the last 35 days. The default retention period is 7 days.   These backups are full daily backups, and transaction logs are also captured throughout the day.

Some key facts:
<ul>
 	<li>All the backups gets deleted when you delete the database itself</li>
 	<li>All backups are stored in S3 for free</li>
 	<li>When doing a point-in-time restore, you don't select snapshot to restore with, instead you choose the db itselt, and select point-in-time restore under actions. RDS will then choose the appropriate snapshot along with transaction logs to restore from.</li>
 	<li>For backups to occur, it's recommended that our db uses a <a href="https://en.wikipedia.org/wiki/Database_engine" rel="nofollow">transactional db engine</a>, e.g. for mysql, use InnoDB. This type of db engine, essentially logs every sql crud queries into log files. This means that are you have restored a snapshot, you can then rewind to a particular transaction (and consequently point-in-time) within the snapshot itself.</li>
 	<li>Backup are taken during a particular window - e.g. 3am in the morning. You can choose when this window is.</li>
 	<li>Backups are performed without stopping the DB, however sql queries are paused to allow the backup to take place. This can cause a slightly longer latency while the backup is occuring.</li>
</ul>
&nbsp;
<h2>Database Snapshots</h2>
These are backups created manually. They exist even after the db is deleted. Hence you have to manually delete them if you no longer need them.

&nbsp;

&nbsp;

&nbsp;

&nbsp;
<h2>Read Replicas</h2>
Unlike Multi-AZ, read replicas are used for scaling.

Read replicas are secondary db's that supports the main db (in the sense that they can be used to respond to read only sql queries). each sql query gets applied to the main db first, and the resulting changes are then  applied to the  replica db. This means  it is asynchronous, rather than synchronous (i.e. simultaneous). Read replica's main purpose is to reduce the load on the primary db instance

Here are the key points about read replicas:
<ul>
 	<li>Only mysql, postgres, aurora, and mariadb offer read replica setup</li>
 	<li>read replicas can be created from other read replicas, although this will cause even more latency. Hence, it's best to create read replicas direct from a db.</li>
 	<li>each main db can have multiple read replicas, which can be in different AZs. This makes read-replicas ideally suited for applications that does db "reading" most  of the time, and comparatively little writing, e.g. wikipedia website. I.e. we can use read replica to effectively scale out read activities. Each DB can have a maximum of 5 read replicas associated with it.</li>
 	<li>We can monitor replication lag between main db and read replicas, using cloudwatch</li>
 	<li>Read replica is only compatible with dbs that uses a transactional db engine. E.g. you have to use InnoDB iwth msyql</li>
 	<li>You can use read replicas to feed data to data warehouses, rather than main db feeding this data. Hence minimises the main db's burden.</li>
 	<li>We can promote a read-replica to become the primary db</li>
 	<li>You must have automated backups enabled to use read replicas.</li>
 	<li>Additinonal benefits that applies to  Mysql only:
<ul>
 	<li>You can create read replicas in different regions, which is useful for high availability, and reduced lags. You can also do this with mariadb too.</li>
 	<li>You can create a read replica for a db that is actually outside of aws, e.g. an on premise db server.</li>
</ul>
</li>
</ul>
When to use read replicas:
<ul>
 	<li>Use when application has a lot of read requests, compared to write requests</li>
 	<li>Provide various systems, e.g. datawarehouse, it's own dedicated read replica to pull data from.</li>
 	<li>importing/exporting data between database outside aws, and a read replica, you can then promote this read replica to become primary db</li>
 	<li>If you wan to do db reindexing. Do this on a read replica, then promote it to become main db, and demote main db to read replica</li>
</ul>
&nbsp;
<h2>Monitoring RDS instances</h2>
There are 2 ways to monitor your RDS setup:
<ul>
 	<li>SNS notifications</li>
 	<li>Cloudwatch</li>
</ul>
<h3>SNS Notifications</h3>
You can easily configure RDS to link up with the SNS service, so that you get notifications when any of the following occurs:
<ul>
 	<li>snapshots</li>
 	<li>parameter group changes</li>
 	<li>option changes</li>
 	<li>Security Group changes</li>
</ul>
<h3></h3>
<h2>Cloudwatch</h2>
Hardware and performance is monitored via  cloudwatch.   For a normal EC2 instances, we need to install a few scripts into our instances to get access to a fuller range of cloudwatch monitors. However with RDS, all these scripts are already set up for you.  This means we use cloudwatch to monitor/track:
<ul>
 	<li>CPU usage</li>
 	<li>ram usage</li>
 	<li>freeable memory (possible with preinstalled scripts)</li>
 	<li>swap usage</li>
 	<li>disk usage</li>
 	<li>read/write IOPS</li>
 	<li>db connections</li>
 	<li>read replicate latency logs</li>
 	<li>read/write throughput</li>
</ul>
&nbsp;

&nbsp;
<h2>Subnet Groups</h2>
As part of  creating an RDS instance, you have to specify which VPC it needs to be created in. Now if you enable multi-AZ deployment (or create read replicas in other AZs), then before creating an RDS instance, you need to first ensure your VPC has subnets attached to it which in turn belongs to different AZs.

However you might have several subnets across different AZs, and you don't want RDS to use all those subnets. In this situation, you need to apply a restriction, and this is done by creating "subnet group":
<p id="PTtRqKA"><a href="http://codingbee.net/wp-content/uploads/2016/02/img_56c0c44fc03c3.png"><img class="alignnone size-full wp-image-6778 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56c0c44fc03c3.png" alt="" /></a></p>
Now you can create a rds db, and specify which vpc, followed by which group of subnets (i.e. subnet group) you want RDS to have access to.

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;
<h2>The drawbacks of RDS compared to EC2 based databases</h2>
<ul>
 	<li>you have less control+freedom, e.g. can't ssh into instance.</li>
 	<li>you can't use some of your db's features, e.g. setting up mysql clusters</li>
</ul>
&nbsp;
<h2>The RDS creation process</h2>
&nbsp;
<p id="nwMmEty"><a href="http://codingbee.net/wp-content/uploads/2016/02/img_56c09b23201ee.png"><img class="alignnone size-full wp-image-6768 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56c09b23201ee.png" alt="" /></a></p>
Here I selected postgresql, then
<p id="KnoDfAM"><a href="http://codingbee.net/wp-content/uploads/2016/02/img_56c09b03be5dc.png"><img class="alignnone size-full wp-image-6767 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56c09b03be5dc.png" alt="" /></a></p>
Notice we can enable   Multi-AZ Deployment feature by simply enabling the correct checkbox.
<p id="gDykmyZ"><a href="http://codingbee.net/wp-content/uploads/2016/02/img_56c09d259b8bc.png"><img class="alignnone size-full wp-image-6771 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56c09d259b8bc.png" alt="" /></a></p>
&nbsp;

Notice you can choose yes/no for multi-AZ.
<p id="JIAEiZd">You also have a long list of isntance types</p>
storage type is either general ssd or provisioned IOPS, or magnetic:
<p id="tCvHDXt"><a href="http://codingbee.net/wp-content/uploads/2016/02/img_56c09d82dfac6.png"><img class="alignnone size-full wp-image-6772 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56c09d82dfac6.png" alt="" /></a></p>
If we end choosing the wrong storage, then it can be easily switched to another (if we have Multi-AZ enabled) with only a max of a couple of minutes downtime.

The next screen is:
<p id="XBdIoIt"><a href="http://codingbee.net/wp-content/uploads/2016/02/img_56c0a39a24d1f.png"><img class="alignnone size-full wp-image-6773 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56c0a39a24d1f.png" alt="" /></a></p>
<p id="mOmTTua"><a href="http://codingbee.net/wp-content/uploads/2016/02/img_56c0a41b18166.png"><img class="alignnone size-full wp-image-6774 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56c0a41b18166.png" alt="" /></a></p>
Once you click on the launch instance button. you get:
<p id="siHJLSN"><a href="http://codingbee.net/wp-content/uploads/2016/02/img_56c0cecdf3854.png"><img class="alignnone size-full wp-image-6780 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56c0cecdf3854.png" alt="" /></a></p>
Notice, that a ip address isn't displayed. Instead we have an endpoint. This is the url we use to connect to the db from a remote ec2 instance. It is unique to our db, and is referred to as a c-name. You can give this name a nicer alias, via route 53.

Now we are ready to connect to this db.

&nbsp;

&nbsp;
<h2>Connecting to RDS db from an EC2 instance</h2>
This is quite straight forward, there are 3 things you need to ensure:
<ul>
 	<li>the security group that is attached to the rds instance is listening on the db's port open, this port can accept source connections from the ec2 instance's private ip address, security group, or subnet range. You just have to specify one of these 3 as the source.</li>
 	<li>The EC2 instance is in the same vpc as the rds</li>
</ul>
&nbsp;

Then you install the relevant db client software, and test your connection, here is an example of what to do inside an ec2 instance:

&nbsp;
<pre># The following link was found on: http://yum.postgresql.org/repopackages.php
$ wget http://yum.postgresql.org/9.5/redhat/rhel-7-x86_64/pgdg-centos95-9.5-2.noarch.rpm
$ yum localinstall pgdg-centos95-9.5-2.noarch.rpm
# the following is the client software only
$ yum install postgresql95-9.5.1   # Note: the server softare is something like: postgresql95-server 
$  psql --version
psql (PostgreSQL) 9.5.1
$ psql -h cb1-postgresql.c2tfibpbfgeb.us-east-1.rds.amazonaws.com -U postgrescb1 -d cb1mydb
Password for user postgrescb1:
psql (9.5.1, server 9.4.5)
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
Type "help" for help.

cb1mydb=&gt;
cb1mydb=&gt; \l
                                      List of databases
   Name    |    Owner    | Encoding |   Collate   |    Ctype    |      Access privileges
-----------+-------------+----------+-------------+-------------+-----------------------------
 cb1mydb   | postgrescb1 | UTF8     | en_US.UTF-8 | en_US.UTF-8 |
 postgres  | postgrescb1 | UTF8     | en_US.UTF-8 | en_US.UTF-8 |
 rdsadmin  | rdsadmin    | UTF8     | en_US.UTF-8 | en_US.UTF-8 | rdsadmin=CTc/rdsadmin
 template0 | rdsadmin    | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/rdsadmin                +
           |             |          |             |             | rdsadmin=CTc/rdsadmin
 template1 | postgrescb1 | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgrescb1             +
           |             |          |             |             | postgrescb1=CTc/postgrescb1
(5 rows)

cb1mydb=&gt; \du
                                  List of roles
   Role name   |                   Attributes                   |    Member of
---------------+------------------------------------------------+-----------------
 postgrescb1   | Create role, Create DB                        +| {rds_superuser}
               | Password valid until infinity                  |
 rds_superuser | Cannot login                                   | {}
 rdsadmin      | Superuser, Create role, Create DB, Replication+| {}
               | Password valid until infinity                  |
 rdsrepladmin  | No inheritance, Cannot login, Replication      | {}
cb1mydb=&gt; \q
$
</pre>
&nbsp;

&nbsp;

https://aws.amazon.com/rds/