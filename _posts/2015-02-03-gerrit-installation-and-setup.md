---
ID: 289
post_title: 'Gerrit &#8211; Installation and setup'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/gerrit/gerrit-installation-and-setup
published: true
post_date: 2015-02-03 00:00:00
---
<h2>Intro</h2>
<a href="https://code.google.com/p/gerrit/">Gerrit</a> is a code review tool. To learn more about it, check out the <a href="http://www.amazon.co.uk/gp/product/1783289473/ref=as_li_qf_sp_asin_il_tl?ie=UTF8&camp=1634&creative=6738&creativeASIN=1783289473&linkCode=as2&tag=codi0f-21">The Gerrit Book</a>. This books has a lot of useful guides including how to automate code review using <a href="http://www.amazon.co.uk/gp/product/1784390089/ref=as_li_qf_sp_asin_il_tl?ie=UTF8&camp=1634&creative=6738&creativeASIN=1784390089&linkCode=as2&tag=codi0f-21">Jenkins</a>. 

This guide covers how to install/setup gerrit on Linux distro, CentOS.


<h2>Set up the database</h2>
Gerrit by default will create the "h2" database for you as part of it's installation process. However if you want to use an alternative/seperate db technology , then this db needs to be created beforehand. In my case I will be using a postgresql db, so I will be referring to postgres for the rest of this article.

You can create install/setup postgresql in a seperate server, or install/setup in the same server that gerrit will be installed on. For resilience purposes it is best practice to have a seperate gerrit-server, and a seperate postgresql-server.

From Puppet automation perspective, the postgresql server needs to be built before the gerrit server. I don't think puppet allows "node ordering", but we can insert a check in the gerrit-puppet-module to check connectivity with postgresql db and error out if that fails, which would happen if postgres server doesn't exist, or if postgres exists but gerrit-server hasn't been authenticated to communicated with the postgres server.

Therefore before I can move on to the next section, <a title="PostgreSQL - Install PostgreSQL, and then create a DB and User Account" href="http://codingbee.net/tutorials/postgresql/postgresql-install-postgresql-and-then-create-a-db-and-user-account/">I need to first install PostgreSQL on a separate server, create a new db, create a new db user account, and finally the db user full privelege to the db.</a>
<h2>Create the Gerrit user</h2>
You need to install gerrit using a user called "gerrit".

You can create the gerrit user in the same way as any other user "using the useradd", command, However it best practice to set the home directory of this user to:
<pre>/opt/gerrit   # this will get created automatically as part of the useradd command. 
</pre>
You need to set a password for the gerrit user, this is not essential but let's you login as gerrit user directly rather than doing "su -" from root user.

Hence, lets create a new user called "gerrit" and set it's home directory to be /opt/gerrit:
<pre>[root@puppetagent02 ~]# useradd -d /opt/gerrit gerrit
[root@puppetagent01 /]#  cat etc/passwd | grep "gerrit"
gerrit:x:501:502::/opt/gerrit:/bin/bash

</pre>
Note, this will create the "/opt" folder along with the "/opt/gerrit" folder.

As root user, you can then run "passwd gerrit" to set a password for gerrit, but we won't bother with that.
<h2>Install dependant packages</h2>
Before you can install gerrit, your machine must have:
<h4>java jdk v1.8</h4>
You can check which version you have like this:
<pre>[root@puppetagent02 etc]$ java -version
openjdk version "1.8.0_31"
OpenJDK Runtime Environment (build 1.8.0_31-b13)
OpenJDK 64-Bit Server VM (build 25.31-b07, mixed mode)
</pre>
if not installed install it via the command line:
<pre>[root@puppetagent02 etc]$ yum install java-1.8.0-openjdk.x86_64</pre>
<h4>Git v1.6+</h4>
You can check this like this:
<pre>[gerrit@puppetagent02 etc]$ git --version
git version 1.7.1
[gerrit@puppetagent02 etc]$
</pre>
If you don't have this, the you can use yum:
<pre>$ yum install git
</pre>
<h4>gitweb</h4>
This is done using:
<pre>$ yum install gitweb
</pre>
Installing gitweb will result in the following file becoming available:

<pre>
/var/lib/gitweb.cgi    # Not sure if this path is correct.
</pre>

You need to keep a note of this so that you can have <a href="https://review.openstack.org/Documentation/config-gitweb.html#_internal_managed_gitweb">gitweb managed internally by gerrit</a>. You then configure <a href="https://git.eclipse.org/r/Documentation/config-gerrit.html#gitweb">gitweb in the gerrit.config</a> file by inserting the following:

<pre>
[gitweb]
    cgi = /var/lib/gitweb.cgi
</pre>

Note: you don't need to change any gitweb specific configurations. 

<h4>Linux utilities</h4>
This is automatically true if installing Gerrit on Linux, but not so on Windows. Hence if you are installing gerrit on a linux machine, then you can ignore this requirement.
<h2>Download the gerrit installation file</h2>
login as the gerrit user:
<pre>[root@puppetagent02 ~]# su - gerrit
[gerrit@puppetagent02 ~]$
</pre>
and downlaod file from:

https://gerrit-releases.storage.googleapis.com/index.html

by using curl:
<pre>[gerrit@puppetagent02 ~]$ pwd
/opt/gerrit
[gerrit@puppetagent02 ~]$ curl https://gerrit-releases.storage.googleapis.com/gerrit-2.10.war -o gerrit.war
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 39.4M  100 39.4M    0     0   853k      0  0:00:47  0:00:47 --:--:-- 1042k
[gerrit@puppetagent02 ~]$ ls -l
total 40392
-rw-rw-r-- 1 gerrit gerrit 41360250 Feb 24 12:23 gerrit.war
</pre>
Note, we download this war into the gerrit user's home directory.
<h2>Install Gerrit</h2>
Note, if you already have a pre-prepared gerrit.config and secure.config, then you can do a silent or semin-automated install. In which case you can skip this section.

You need to be logged in as the gerrit user to Install this war:
<pre>[gerrit@puppetagent01 ~]$ id
uid=501(gerrit) gid=502(gerrit) groups=502(gerrit)
[gerrit@puppetagent01 ~]$ pwd
/opt/gerrit
[gerrit@puppetagent01 ~]$ ls
gerrit.war
[gerrit@puppetagent01 ~]$ java -jar gerrit.war
Gerrit Code Review
usage: java -jar gerrit.war command [ARG ...]

The most commonly used commands are:
  init           Initialize a Gerrit installation
  reindex        Rebuild the secondary index
  daemon         Run the Gerrit network daemons
  gsql           Run the interactive query console
  version        Display the build version number

  ls             List files available for cat
  cat FILE       Display a file from the archive
</pre>
Here we there are a number of options of which we are going to use init:

Note: run the "init" command as the gerrit user.
<pre>[gerrit@puppetagent01 ~]$ java -jar gerrit.war init     # Run the "init" command as the gerrit user. 

*** Gerrit Code Review 2.10
***


*** Git Repositories
***

Location of Git repositories   [git]:

</pre>
If you accept the "git" defaults, then it will create the following folder:
<pre>[root@puppetagent01 gerrit]# tree
.
├── bin
├── data
├── etc
│     └── mail
├── gerrit.war
├── lib
├── logs
├── plugins
├── static
└── tmp

9 directories, 1 file
[root@puppetagent01 gerrit]# tree
.
├── bin
├── data
├── etc
│     └── mail
├── gerrit.war
├── git                       # this folder is now created. 
├── lib
├── logs
├── plugins
├── static
└── tmp

10 directories, 1 file
[root@puppetagent01 gerrit]#

</pre>
Next we need to do accept the default "h2":

This will result in the "db" folder being created:
<pre>[root@puppetagent01 gerrit]# tree
.
├── bin
├── data
├── db             # this folder gets created. 
├── etc
│     └── mail
├── gerrit.war
├── git
├── lib
├── logs
├── plugins
├── static
└── tmp

11 directories, 1 file
[root@puppetagent01 gerrit]#
</pre>
Now we accept the default "LUCENE licence". This doesnt create any new files/folders.

The same is true of "OPENID", note this includes "?" option which might prompt you to try a few things.

Keep accepting defaults, when you reach and say yes to the following...:
<pre>Copy gerrit.war to /opt/gerrit/bin/gerrit.war [Y/n]? Y
</pre>
You get the following new item:
<pre>[root@puppetagent01 gerrit]# tree
.
├── bin
│     └── gerrit.war          # new file here. 
├── data
├── db
├── etc
│     └── mail
├── gerrit.war
├── git
├── lib
├── logs
├── plugins
├── static
└── tmp

11 directories, 2 files
[root@puppetagent01 gerrit]#
</pre>
Also for now, select "no" for the "bouncy castle" option. By selecting no, this will create the following folder:
<pre>[root@puppetagent01 gerrit]# tree
.
├── bin
│     └── gerrit.war
├── data
├── db
├── etc
│     ├── mail
│     └── ssh_host_key   # this file is created. 
├── gerrit.war
├── git
├── lib
├── logs
├── plugins
├── static
└── tmp

11 directories, 3 files
</pre>
Now it will give you the option to install/uninstall various plugins. These are temporarily placed in the local tmp folder:
<pre>
[root@puppetagent01 gerrit]# tree
.
├── bin
│     └── gerrit.war
├── cache
├── data
├── db
├── etc
│     ├── mail
│     └── ssh_host_key
├── gerrit.war
├── git
├── lib
├── logs
├── plugins
├── static
└── tmp
    ├── plugin_download-commands_150203_1501_1069983237733257196.jar
    ├── plugin_replication_150203_1501_3947389358378156958.jar
    ├── plugin_reviewnotes_150203_1501_7127427235158661758.jar
    └── plugin_singleusergroup_150203_1501_439039537351044470.jar

12 directories, 7 files


</pre>
Now when we select no to each of these, they will get deleted from this directory.

However if we say yes to any of them, then they get moved to the plugins folder:
<pre>[root@puppetagent01 gerrit]# tree
.
├── bin
│     └── gerrit.war
├── cache
├── data
├── db
├── etc
│     ├── mail
│     └── ssh_host_key
├── gerrit.war
├── git
├── lib
├── logs
├── plugins
│     └── download-commands.jar    # we said yes to this plugin. That's why it is here. 
├── static
└── tmp
    ├── plugin_replication_150203_1501_3947389358378156958.jar
    ├── plugin_reviewnotes_150203_1501_7127427235158661758.jar
    └── plugin_singleusergroup_150203_1501_439039537351044470.jar

12 directories, 7 files
[root@puppetagent01 gerrit]#


</pre>
Here are the values I entered during the interactive prompts of my install:
<pre>[gerrit@puppetagent01 ~]$ java -jar gerrit.war init   # Run the "init" command as the gerrit user. 

*** Gerrit Code Review 2.10
***


*** Git Repositories
***

Location of Git repositories   [git]:

*** SQL Database
***

Database server type           [h2]:

*** Index
***

Type                           [LUCENE/?]:

The index must be rebuilt before starting Gerrit:
  java -jar gerrit.war reindex -d site_path

*** User Authentication
***

Authentication method          [OPENID/?]:

*** Review Labels
***

Install Verified label         [y/N]? y

*** Email Delivery
***

SMTP server hostname           [localhost]:
SMTP server port               [(default)]:
SMTP encryption                [NONE/?]:
SMTP username                  :

*** Container Process
***

Run as                         [gerrit]:
Java runtime                   [/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.31-1.b13.el6_6.x86_64/jre]:
Copy gerrit.war to /opt/gerrit/bin/gerrit.war [Y/n]? Y
Copying gerrit.war to /opt/gerrit/bin/gerrit.war

*** SSH Daemon
***

Listen on address              [*]:
Listen on port                 [29418]:

Gerrit Code Review is not shipped with Bouncy Castle Crypto SSL v149
  If available, Gerrit can take advantage of features
  in the library, but will also function without it.
Download and install it now [Y/n]? n
Generating SSH host key ... rsa(simple)... done

*** HTTP Daemon
***

Behind reverse proxy           [y/N]?
Use SSL (https://)             [y/N]?
Listen on address              [*]:
Listen on port                 [8080]:
Canonical URL                  [http://puppetagent01.co.uk:8080/]:

*** Plugins
***

Install plugin commit-message-length-validator version v2.10 [y/N]? N
Install plugin download-commands version v2.10 [y/N]? y
Install plugin replication version v2.10 [y/N]? n
Install plugin reviewnotes version v2.10 [y/N]? N
Install plugin singleusergroup version v2.10 [y/N]? N

Initialized /opt/gerrit
[gerrit@puppetagent01 ~]$

</pre>
All the info that you entered via the interactive mode (with exception of password data), is used to generate a the following config files:
<pre>[gerrit@puppetagent02 etc]$ pwd
/opt/gerrit/etc
[gerrit@puppetagent02 etc]$ ls -l
total 16
-rw-rw-r-- 1 gerrit gerrit  650 Feb 13 07:07 gerrit.config      # this file
drwxrwxr-x 2 gerrit gerrit 4096 Feb 11 11:52 mail
-rw------- 1 gerrit gerrit  215 Feb 11 11:52 secure.config      # this stores the ldab and db credentials
-rw------- 1 gerrit gerrit 1219 Feb 11 11:52 ssh_host_key
[gerrit@puppetagent02 etc]$
</pre>
These 2 files are useful if you want install gerrit without any interactive prompts (i.e. a silent gerrit install), or semi-automated install, which involves just accepting the defaults.

There is a huge amount various configurations that you can set in the gerrit.config file, to see them, checkout the <a href="https://gerrit-review.googlesource.com/Documentation/config-gerrit.html">gerrit.config documentation</a>.
<h2>Silent or Semi-automated Gerrit Instalation</h2>
To do the silent install, You first need to place your gerrit.war and pre-prepared gerrit.config file in the following folder:
<pre>[gerrit@puppetagent02 ~]$ pwd
/opt/gerrit
[gerrit@puppetagent02 ~]$ ls -l
total 40400
-rwxrwxr-x 1 gerrit gerrit      650 Feb 24 12:55 gerrit.config
-rw-rw-r-- 1 gerrit gerrit 41360250 Feb 24 12:54 gerrit.war
-rwxrwxr-x 1 gerrit gerrit      215 Feb 24 12:55 secure.config
</pre>
Now, if you are doing the installation on a VM, then now is a good time to take a <strong>VM snapshot</strong>!!!

After that you need to go into that directory and run the following to do a silent install:
<pre>java -jar gerrit.war init --batch --site-path /opt/gerrit
</pre>
Note: you omit "--batch" if you want to do a semi-automated install. Where you can see each of them settings and default values.

Here's an example of this:
<pre>[gerrit@puppetagent02 ~]$ pwd
/opt/gerrit
[gerrit@puppetagent02 ~]$ ls -l
total 40396
-rwxr-xr-x 1 root root      488 Feb 10 14:11 gerrit.config
-rw-r--r-- 1 root root 41360250 Feb  4 09:09 gerrit.war
-rw-r--r-- 1 root root      215 Feb  4 09:09 secure.config
[gerrit@puppetagent02 ~]$ java -jar gerrit.war init --batch --site-path /opt/gerrit
Generating SSH host key ... rsa(simple)... done
Initialized /opt/gerrit
</pre>
After that has run, you should find your config files here now:
<pre>
[gerrit@puppetagent02 etc]$ pwd
/opt/gerrit/etc
[gerrit@puppetagent02 etc]$ ls -l
total 16
-rwxrwxr-x 1 gerrit gerrit  650 Feb 24 12:55 gerrit.config
drwxrwxr-x 2 gerrit gerrit 4096 Feb 24 13:05 mail
-rw------- 1 gerrit gerrit  170 Feb 24 13:06 secure.config
-rw------- 1 gerrit gerrit 1219 Feb 24 13:05 ssh_host_key

</pre>
Note: The content on the secure.config may have changed during the installation, if so then overwrite the secure.config file with your pre-prepared config file.
<pre>[gerrit@puppetagent02 etc]$ pwd
/opt/gerrit/etc
[gerrit@puppetagent02 etc]$ ls -l
total 16
-rwxrwxr-x 1 gerrit gerrit  650 Feb 24 12:55 gerrit.config
drwxrwxr-x 2 gerrit gerrit 4096 Feb 24 13:05 mail
-rw------- 1 gerrit gerrit  170 Feb 24 13:06 secure.config
-rw------- 1 gerrit gerrit 1219 Feb 24 13:05 ssh_host_key
[gerrit@puppetagent02 etc]$ cp /tmp/secure.config .
[gerrit@puppetagent02 etc]$ ls -l
total 16
-rwxrwxr-x 1 gerrit gerrit  650 Feb 24 12:55 gerrit.config
drwxrwxr-x 2 gerrit gerrit 4096 Feb 24 13:05 mail
-rw------- 1 gerrit gerrit  215 Feb 24 13:09 secure.config
-rw------- 1 gerrit gerrit 1219 Feb 24 13:05 ssh_host_key
[gerrit@puppetagent02 etc]$
</pre>
In my case I had kept a backup of my secure.config file in the /tmp/ folder.
<h2>Convert the Gerrit shell script into a service</h2>
Return back to the root user:
<pre>[gerrit@puppetagent02 etc]$ exit
logout
[root@puppetagent02 ~]#
</pre>
This was done by first running the following commands to create symbolic links:

Note: creating symbolic link isn't the best approach. So please ignore this.
<pre>sudo ln -snf /opt/gerrit/bin/gerrit.sh /etc/init.d/gerrit   # ignore this
</pre>
Note: I think you need to do this as the root user.

Instead of creating the symbolic link, as shown above, actually make a copy of this file (and get rid of the ".sh" suffix):
<pre>[root@puppetagent02 /]# cp /opt/gerrit/bin/gerrit.sh /etc/init.d/gerrit
</pre>
Make sure it has execute permission:
<pre>[root@puppetagent02 init.d]# ls -l gerrit
total 248
-rwxr-xr-x  1 root root 14638 Feb 24 15:09 gerrit
</pre>
Next run:
<pre>[root@puppetagent02 init.d]# chkconfig --add gerrit
</pre>
After that you should be able to do:
<pre>[root@puppetagent02 init.d]# chkconfig --list gerrit
gerrit          0:off   1:off   2:on    3:on    4:on    5:on    6:off
</pre>
Then start up on all run-levels, you do:
<pre>[root@puppetagent02 init.d]# chkconfig --level 0123456 gerrit on
[root@puppetagent02 init.d]# chkconfig --list gerrit
gerrit          0:on    1:on    2:on    3:on    4:on    5:on    6:on
[root@puppetagent02 init.d]#
</pre>
Now let's try using the service command, and you might get the following error message:
<pre>[gerrit@puppetagent02 etc]$ service gerrit status
** ERROR: GERRIT_SITE not set
[gerrit@puppetagent02 etc]$ 
</pre>
If you look inside the actual gerrit.sh script, it advises you to create the file, /etc/default/gerritcodereview, and store any gerrit specific environment variables in it, i.e.:
<pre>[root@puppetagent02 default]# pwd
/etc/default
[root@puppetagent02 default]# ls -l
total 8
-rw-r--r--. 1 root root 1756 Nov 21  2013 nss
-rw-------. 1 root root  119 Oct 13  2011 useradd
[root@puppetagent02 default]# echo "GERRIT_SITE=/opt/gerrit" &gt; /etc/default/gerritcodereview
[root@puppetagent02 default]# ls -l
total 12
-rw-r--r--  1 root root   24 Feb 24 13:16 gerritcodereview
-rw-r--r--. 1 root root 1756 Nov 21  2013 nss
-rw-------. 1 root root  119 Oct 13  2011 useradd
[root@puppetagent02 default]#

</pre>
Note: you need to do this as the root user, since the gerrit user only has read permissions for this folder.

Now if we try the service command again:
<pre>[root@puppetagent02 default]# service gerrit status
Checking arguments to Gerrit Code Review:
  GERRIT_SITE     =  /opt/gerrit
  GERRIT_CONFIG   =  /opt/gerrit/etc/gerrit.config
  GERRIT_PID      =  /opt/gerrit/logs/gerrit.pid
  GERRIT_TMP      =  /opt/gerrit/tmp
  GERRIT_WAR      =  /opt/gerrit/bin/gerrit.war
  GERRIT_FDS      =  1024
  GERRIT_USER     =  gerrit
  JAVA            =  /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.31-1.b13.el6_6.x86_64/jre/bin/java
  JAVA_OPTIONS    =
  RUN_EXEC        =  /usr/bin/perl -e '$x=$ENV{JAVA};exec $x @ARGV;die $!' -- GerritCodeReview
  RUN_ARGS        =  -jar /opt/gerrit/bin/gerrit.war daemon -d /opt/gerrit

[root@puppetagent02 default]#
</pre>
Success!!!

Now we need to start the service:
<pre>[root@puppetagent02 default]# service gerrit start
Starting Gerrit Code Review: FAILED
</pre>
However if it fails to start, for example above, then check gerrit's error log, which you can find here:
<pre>[gerrit@puppetagent02 logs]$ pwd
/opt/gerrit/logs
[gerrit@puppetagent02 logs]$ ls error_log
error_log
[gerrit@puppetagent02 logs]$
</pre>
A common error you might have is to do with reindexing, which we'll cover next:
<h2>do the reindexing</h2>
This step is optional and you may need to do this if your gerrit service failed to start. Check your gerrit logfile:
<pre>cat /opt/gerrit/logs/error_log</pre>
If it contains the following error message:
<pre>1) No index versions ready; run Reindex
</pre>
Then you fix this by doing a <a href="https://gerrit-documentation.storage.googleapis.com/Documentation/2.10/pgm-reindex.html">reindexing</a>, which is done by simply running the following command:
<pre>[gerrit@puppetagent02 ~]$ java -jar gerrit.war reindex
</pre>
Then try starting the service again:
<pre>
[gerrit@puppetagent02 ~]$ service gerrit start
Starting Gerrit Code Review: OK
[gerrit@puppetagent02 ~]$ service gerrit status
Checking arguments to Gerrit Code Review:
  GERRIT_SITE     =  /opt/gerrit
  GERRIT_CONFIG   =  /opt/gerrit/etc/gerrit.config
  GERRIT_PID      =  /opt/gerrit/logs/gerrit.pid
  GERRIT_TMP      =  /opt/gerrit/tmp
  GERRIT_WAR      =  /opt/gerrit/bin/gerrit.war
  GERRIT_FDS      =  1024
  GERRIT_USER     =  gerrit
  JAVA            =  /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.31-1.b13.el6_6.x86_64/jre/bin/java
  JAVA_OPTIONS    =
  RUN_EXEC        =  /usr/bin/perl -e '$x=$ENV{JAVA};exec $x @ARGV;die $!' -- GerritCodeReview
  RUN_ARGS        =  -jar /opt/gerrit/bin/gerrit.war daemon -d /opt/gerrit

Gerrit running pid=10885
[gerrit@puppetagent02 ~]$


</pre>
Success!!!
<h2>Start Gerrit at boot time for all Run Levels</h2>
We can also now manage the gerrit service via the service command:
<pre>[gerrit@puppetagent02 bin]$ service gerrit stop
Stopping Gerrit Code Review: OK             # after this the gerrit web insteface also stops working.
[gerrit@puppetagent02 bin]$ service gerrit start
Starting Gerrit Code Review: OK
</pre>
One final check we need to do is check that the gerrit service is registered correctly with chkconfig:
<pre>[gerrit@puppetagent02 bin]$ chkconfig
auditd          0:off   1:off   2:on    3:on    4:on    5:on    6:off
blk-availability        0:off   1:on    2:on    3:on    4:on    5:on    6:off
cgconfig        0:off   1:off   2:off   3:off   4:off   5:off   6:off
cgred           0:off   1:off   2:off   3:off   4:off   5:off   6:off
crond           0:off   1:off   2:on    3:on    4:on    5:on    6:off
docker          0:off   1:off   2:on    3:on    4:on    5:on    6:off
<strong>gerrit          0:off   1:off   2:off   3:on    4:off   5:off   6:off</strong>
ip6tables       0:off   1:off   2:on    3:on    4:on    5:on    6:off
iptables        0:off   1:off   2:on    3:on    4:on    5:on    6:off
lvm2-monitor    0:off   1:on    2:on    3:on    4:on    5:on    6:off
lxc             0:off   1:off   2:off   3:off   4:off   5:off   6:off
mcollective     0:off   1:off   2:on    3:on    4:on    5:on    6:off
netconsole      0:off   1:off   2:off   3:off   4:off   5:off   6:off
netfs           0:off   1:off   2:off   3:on    4:on    5:on    6:off
network         0:off   1:off   2:on    3:on    4:on    5:on    6:off
ntpd            0:off   1:off   2:off   3:off   4:off   5:off   6:off
ntpdate         0:off   1:off   2:off   3:off   4:off   5:off   6:off
postfix         0:off   1:off   2:on    3:on    4:on    5:on    6:off
puppet          0:off   1:off   2:off   3:off   4:off   5:off   6:off
puppetmaster    0:off   1:off   2:off   3:off   4:off   5:off   6:off
rdisc           0:off   1:off   2:off   3:off   4:off   5:off   6:off
restorecond     0:off   1:off   2:off   3:off   4:off   5:off   6:off
rhnsd           0:off   1:off   2:on    3:on    4:on    5:on    6:off
rsyslog         0:off   1:off   2:on    3:on    4:on    5:on    6:off
saslauthd       0:off   1:off   2:off   3:off   4:off   5:off   6:off
sshd            0:off   1:off   2:on    3:on    4:on    5:on    6:off
udev-post       0:off   1:on    2:on    3:on    4:on    5:on    6:off
vboxadd         0:off   1:off   2:on    3:on    4:on    5:on    6:off
vboxadd-service 0:off   1:off   2:on    3:on    4:on    5:on    6:off
vboxadd-x11     0:off   1:off   2:off   3:on    4:off   5:on    6:off
[gerrit@puppetagent02 bin]$
</pre>
According to this, the gerrit service should start up automatically when the machine is rebooted (and starts up under run level 3).

let's now reboot the machine. After the reboot, let's check whether the gerrit service is running:
<pre>[gerrit@puppetagent02 ~]$ service gerrit status
Checking arguments to Gerrit Code Review:
  GERRIT_SITE     =  /opt/gerrit
  GERRIT_CONFIG   =  /opt/gerrit/etc/gerrit.config
  GERRIT_PID      =  /opt/gerrit/logs/gerrit.pid
  GERRIT_TMP      =  /opt/gerrit/tmp
  GERRIT_WAR      =  /opt/gerrit/bin/gerrit.war
  GERRIT_FDS      =  1024
  GERRIT_USER     =  gerrit
  JAVA            =  /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.31-1.b13.el6_6.x86_64/jre/bin/java
  JAVA_OPTIONS    =
  RUN_EXEC        =  /usr/bin/perl -e '$x=$ENV{JAVA};exec $x @ARGV;die $!' -- GerritCodeReview
  RUN_ARGS        =  -jar /opt/gerrit/bin/gerrit.war daemon -d /opt/gerrit

Gerrit running pid=1312

</pre>
Success!!

Just in case the machine is reboot at a different run level, it may be best to ensure that gerrit starts up in all run levels, which can ensure by running the following command (as root user):
<pre>[root@puppetagent02 ~]# chkconfig | grep gerrit
gerrit          0:off   1:off   2:off   3:on    4:off   5:off   6:off
[root@puppetagent02 ~]# chkconfig --level 0123456 gerrit on
[root@puppetagent02 ~]# chkconfig | grep gerrit
gerrit          0:on    1:on    2:on    3:on    4:on    5:on    6:on
[root@puppetagent02 ~]#
</pre>
This is so that the script can be managed via the linux "service" command, e.g. "service gerrit status" and also this will let you configure gerrit to start-up during boot-time.
<h2>install Gerrit plugins</h2>
There are a bunch of official plugins available here:

https://gerritcodereview-plugins.storage.googleapis.com/index.html

If you want to install/load any of these plugins, you just need to copy the jar files into the following folder:
<pre>[gerrit@puppetagent02 plugins]$ pwd
/opt/gerrit/plugins/
</pre>
After that restart the gerrit service:
<h2>start the gerrit service</h2>
Now start the gerrit service:
<pre>/opt/gerrit/bin/gerrit.sh start
</pre>
Confirm that service is running:
<pre>[gerrit@puppetagent01 bin]$ /opt/gerrit/bin/gerrit.sh status
Checking arguments to Gerrit Code Review:
  GERRIT_SITE     =  /opt/gerrit
  GERRIT_CONFIG   =  /opt/gerrit/etc/gerrit.config
  GERRIT_PID      =  /opt/gerrit/logs/gerrit.pid
  GERRIT_TMP      =  /opt/gerrit/tmp
  GERRIT_WAR      =  /opt/gerrit/bin/gerrit.war
  GERRIT_FDS      =  1024
  GERRIT_USER     =  gerrit
  JAVA            =  /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.31-1.b13.el6_6.x86_64/jre/bin/java
  JAVA_OPTIONS    =
  RUN_EXEC        =  /usr/bin/perl -e '$x=$ENV{JAVA};exec $x @ARGV;die $!' -- GerritCodeReview
  RUN_ARGS        =  -jar /opt/gerrit/bin/gerrit.war daemon -d /opt/gerrit

Gerrit running pid=15768
[gerrit@puppetagent01 bin]$

</pre>
Here's a different init setup that I also tried out:
<pre>[gerrit@puppetagent02 ~]$ java -jar gerrit.war init   # Note: run the "init" command as the gerrit user. 

*** Gerrit Code Review 2.10
***


*** Git Repositories
***

Location of Git repositories   [git]:

*** SQL Database
***

Database server type           [h2]:

*** Index
***

Type                           [LUCENE/?]: ?
       Supported options are:
         lucene
         solr
Type                           [LUCENE/?]: solr
Solr Index URL                 [localhost:9983]:

The index must be rebuilt before starting Gerrit:
  java -jar gerrit.war reindex -d site_path

*** User Authentication
***

Authentication method          [OPENID/?]: ?
       Supported options are:
         openid
         openid_sso
         http
         http_ldap
         client_ssl_cert_ldap
         ldap
         ldap_bind
         custom_extension
         development_become_any_account
Authentication method          [OPENID/?]: development_become_any_account

*** Review Labels
***

Install Verified label         [y/N]? y

*** Email Delivery
***

SMTP server hostname           [localhost]:
SMTP server port               [(default)]:
SMTP encryption                [NONE/?]: ?
       Supported options are:
         none
         ssl
         tls
SMTP encryption                [NONE/?]:
SMTP username                  : gerrit
gerrit's password              :
              confirm password :

*** Container Process
***

Run as                         [gerrit]: ?
Java runtime                   [/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.31-1.b13.el6_6.x86_64/jre]:
Copy gerrit.war to /opt/gerrit/bin/gerrit.war [Y/n]? Y
Copying gerrit.war to /opt/gerrit/bin/gerrit.war

*** SSH Daemon
***

Listen on address              [*]:
Listen on port                 [29418]:

Gerrit Code Review is not shipped with Bouncy Castle Crypto SSL v149
  If available, Gerrit can take advantage of features
  in the library, but will also function without it.
Download and install it now [Y/n]? Y
Downloading http://www.bouncycastle.org/download/bcpkix-jdk15on-149.jar ... OK
Checksum bcpkix-jdk15on-149.jar OK

Gerrit Code Review is not shipped with Bouncy Castle Crypto Provider v149
** This library is required by Bouncy Castle Crypto SSL v149. **
Download and install it now [Y/n]? Y
Downloading http://www.bouncycastle.org/download/bcprov-jdk15on-149.jar ... OK
Checksum bcprov-jdk15on-149.jar OK
Generating SSH host key ... rsa... dsa... done

*** HTTP Daemon
***

Behind reverse proxy           [y/N]? N
Use SSL (https://)             [y/N]? y
Listen on address              [*]:
Listen on port                 [8443]:
Canonical URL                  [https://puppetagent02.co.uk:8443/]:
Create new self-signed SSL certificate [Y/n]? Y
Certificate server name        [puppetagent02.co.uk]:
Certificate expires in (days)  [365]:

*** Plugins
***

Install plugin commit-message-length-validator version v2.10 [y/N]?
Install plugin download-commands version v2.10 [y/N]?
Install plugin replication version v2.10 [y/N]?
Install plugin reviewnotes version v2.10 [y/N]?
Install plugin singleusergroup version v2.10 [y/N]?

Initialized /opt/gerrit
[gerrit@puppetagent02 ~]$


</pre>
<h3>Supported Databases</h3>
If you want to see a list of supported databases. then you can view them during the (init) installation process by typing "?" when prompted to choose a db:
<pre>[root@puppetagent02 gerrit]# java -jar gerrit.war init

*** Gerrit Code Review 2.10
***


*** Git Repositories
***

Location of Git repositories   [git]:

*** SQL Database
***

Database server type           [h2]: asdfasdf
error: 'asdfasdf' is not a valid choice
       Supported options are:
         h2
         jdbc
         maxdb
         mysql
         oracle
         postgresql
Database server type           [h2]: ?
       Supported options are:
         h2
         jdbc
         maxdb
         mysql
         oracle
         postgresql
Database server type           [h2]:

</pre>
<pre>LDAP</pre>
here are the prompts you get when choosing the ldap option:
<pre>*** User Authentication
***

Authentication method          [OPENID/?]: ?
       Supported options are:
         openid
         openid_sso
         http
         http_ldap
         client_ssl_cert_ldap
         ldap                                # I selected this one
         ldap_bind
         custom_extension
         development_become_any_account
Authentication method          [OPENID/?]: ldap
LDAP server                    [ldap://localhost]:    # I think need to also include port number.
LDAP username                  : test
test's password                :
              confirm password :
Account BaseDN                 : test
Group BaseDN                   [test]: test



</pre>
Here we have

Note: the db password details are not stored in the gerrit.config, instead it is stored a file called secure.config, which sits alongside the gerrit.config:
<pre>[root@puppetagent02 etc]# pwd
/opt/gerrit/etc
[root@puppetagent02 etc]# ls -l
total 16
-rw-r--r-- 1 root root  539 Feb  5 07:34 gerrit.config
drwxr-xr-x 2 root root 4096 Feb  5 07:34 mail
-rw------- 1 root root  191 Feb  5 07:34 secure.config
-rw------- 1 root root 1220 Feb  5 07:33 ssh_host_key
[root@puppetagent02 etc]#

</pre>
Automating Gerrit install:

In the above examples, we had an interactive prompt which we had to enter values into. In the end, this resulted in the gerrit installation as well as creation of the gerrit.config file. This config file contains all the information that you entered during the interactive prompts.

If you already have a gerrit.config file, then it is possible to do a "silent install", i.e. automate the installation using an existing gerrit.config file.

Then start gerrit (you might need to reindex again):
<pre>[gerrit@puppetagent02 ~]$ /opt/gerrit/bin/gerrit.sh start
Starting Gerrit Code Review: OK
</pre>
Next, you can check that the gerrit process is running under the gerrit user:
<pre>[gerrit@puppetagent02 ~]$ ps -ef | grep "GerritCodeReview" | grep -v "grep"
gerrit   10853     1 14 14:37 pts/0    00:00:16 GerritCodeReview -jar /opt/gerrit/bin/gerrit.war daemon -d /opt/gerrit --run-id=1423579028.10832
[gerrit@puppetagent02 ~]$

</pre>
This process id (pid) should match up with:
<pre>[gerrit@puppetagent02 ~]$ /opt/gerrit/bin/gerrit.sh status
Checking arguments to Gerrit Code Review:
  GERRIT_SITE     =  /opt/gerrit
  GERRIT_CONFIG   =  /opt/gerrit/etc/gerrit.config
  GERRIT_PID      =  /opt/gerrit/logs/gerrit.pid
  GERRIT_TMP      =  /opt/gerrit/tmp
  GERRIT_WAR      =  /opt/gerrit/bin/gerrit.war
  GERRIT_FDS      =  1024
  GERRIT_USER     =  gerrit
  JAVA            =  /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.31-1.b13.el6_6.x86_64/jre/bin/java
  JAVA_OPTIONS    =
  RUN_EXEC        =  /usr/bin/perl -e '$x=$ENV{JAVA};exec $x @ARGV;die $!' -- GerritCodeReview
  RUN_ARGS        =  -jar /opt/gerrit/bin/gerrit.war daemon -d /opt/gerrit

Gerrit running pid=10853
[gerrit@puppetagent02 ~]$
</pre>
Tip:

when testing your gerrit web interface on your local machine, you will need to add the following to your window's host file:
<pre>C:\Windows\System32\drivers\etc\hosts</pre>
Here you add something like:
<pre>{gerrit-server-ip-number}   hostname.co.uk 
</pre>
see also:

http://gerrit-documentation.googlecode.com/svn/Documentation/2.7/install.html - this explains how to convert the gerrit script into a service.

https://review.gerrithub.io/Documentation/install.html
https://gerrit-documentation.storage.googleapis.com/Documentation/2.10/cmd-index.html
http://gerrit-documentation.googlecode.com/svn/Documentation/2.7/install.html

https://gerrit.libreoffice.org/Documentation/install-quick.html

https://gerrit-review.googlesource.com/Documentation/#_tutorial

http://stackoverflow.com/questions/8584660/how-to-login-gerrit-as-administrator

http://www.google.co.uk/search?hl=en-GB&amp;ie=UTF-8&amp;source=android-browser&amp;q=gerrit+rest&amp;gfe_rd=cr&amp;ei=ozTdVJWfAcnBWJTMgJgJ#safe=off&amp;hl=en-GB&amp;q=gerrit+rest+ssh

https://gerrit-documentation.storage.googleapis.com/Documentation/2.10/cmd-plugin-install.html#_scripting

https://gerrit-documentation.storage.googleapis.com/Documentation/2.10/cmd-index.html

https://gerrit-documentation.storage.googleapis.com/Documentation/2.10/cmd-plugin-reload.html

https://gerrit-documentation.storage.googleapis.com/Documentation/2.10/cmd-plugin-ls.html

https://gerrit-review.googlesource.com/Documentation/config-gerrit.html#plugins

https://gerrit-documentation.storage.googleapis.com/Documentation/2.10/cmd-plugin-install.html

https://gerrit-review.googlesource.com/Documentation/config-gerrit.html