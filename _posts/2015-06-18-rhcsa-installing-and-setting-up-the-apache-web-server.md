---
ID: 417
post_title: 'RHCSA &#8211; Installing and setting up the Apache web server'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-installing-and-setting-up-the-apache-web-server
published: true
post_date: 2015-06-18 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:
[accordion]
[toggle title="What is the command to install apache?"]
$ yum install httpd
[/toggle]
[toggle title="What is the main config file of this file?"]
/etc/httpd/conf/httpd.conf
[/toggle]
[toggle title="Which folder do you store all your web content files, html files, css files,...etc?"]
/var/www/html
[/toggle]
[toggle title="What is the command to start the apache service?"]
$ systemctl start httpd
[/toggle]
[toggle title="What ports does this service listen on?"]
port 80  - for httpd
prot 443 - for httpds
[/toggle]
[toggle title="What is the command to add an 'allow' firewall rule so that the apache service can listen on these port?"]
$ firewall-cmd --add-service=http --permanent
$ firewall-cmd --add-service=https --permanent
$ systemctl restart firewalld
[/toggle]
[toggle title="What is the command to view your apaches, selinux allow mapping rules, context assignment rules, and boolean settings?"]
$ sesearch --allow | grep httpd
$ semanage {subcommand} -l | grep httpd
$ getsebool -a | grep httpd
[/toggle]
[toggle title="What is the command to view apache related log messages?"]
$ journalctl -xu httpd.service
[/toggle]
[toggle title="What is the command to view potential apache related SELinux error messages?"]
$ sealert -a /var/log/audit/audit.log
$ sealert -a /var/log/messages
[/toggle]
[toggle title="How do you check your web server is working?"]
Open firefox and go to "http://localhost"
[/toggle]




One of the most common reasons for having a RHEL server is so to set it up as a web server to host websites that other people can access via the internet. In order to set up a website as a web server, you need to install a web server software your machine. The "Apache HTTP Server" is the most commonly web server software used worldwide.  

In the main public yum repos, the apache software goes by the name, "httpd". So the first thing we need to do is install this software:


<pre>
$ yum install httpd
</pre>


httpd also comes with a seperate user manual that you can also install:


<pre>
$ yum install httpd-manual
</pre>

This is actually a web based manual, and to gain access to it, first restart the httpd service:

<pre>
$ systemctl restart httpd.service
</pre>

Then open a web browser and go to:

<pre>
httpd://localhost/manual
</pre>

Now let's see where all the httpd's config files are located:


<pre>
$ rpm -qc httpd
/etc/httpd/conf.d/autoindex.conf
/etc/httpd/conf.d/userdir.conf
/etc/httpd/conf.d/welcome.conf
/etc/httpd/conf.modules.d/00-base.conf
/etc/httpd/conf.modules.d/00-dav.conf
/etc/httpd/conf.modules.d/00-lua.conf
/etc/httpd/conf.modules.d/00-mpm.conf
/etc/httpd/conf.modules.d/00-proxy.conf
/etc/httpd/conf.modules.d/00-systemd.conf
/etc/httpd/conf.modules.d/01-cgi.conf
<strong>/etc/httpd/conf/httpd.conf</strong>
/etc/httpd/conf/magic
/etc/logrotate.d/httpd
/etc/sysconfig/htcacheclean
/etc/sysconfig/httpd
</pre>

httpd's main config file is the httpd.conf file, which I have highlighted in bold above. This file contains a lot of comments to help you configure the file.

A couple of the main setting are 

<pre>
$ grep '^DocumentRoot' /etc/httpd/conf/httpd.conf
DocumentRoot "/var/www/html"
</pre>

<pre>
$ grep '^Listen' /etc/httpd/conf/httpd.conf
Listen 80
</pre>


The DocumentRoot setting shows where you need to store your websites files, e.g. html, css, php, javascript,...and etc. 'Listen' sets what ports to listen on.  


Another handy software that you could use is, "elinks", which is command line based web browser. We can use this to test whether our web server is working from the command line: 


<pre>
$ yum install elinks
</pre>


Here's how you can use elinks:



<pre>
$ elinks --help
ELinks 0.12pre6

Usage: elinks [OPTION]... [URL]...

Options:
  -anonymous [0|1]      Restrict to anonymous mode
  -auto-submit [0|1]    Autosubmit first form
  -base-session <num>   Clone internal session with given ID
  -config-dir <str>     Name of directory with configuration file
  -config-dump          Print default configuration file to stdout
  -config-file <str>    Name of configuration file
  -config-help          Print help for configuration options
  -default-mime-type    MIME type assumed for unknown document types
  -default-keys [0|1]   Ignore user-defined keybindings
  -dump [0|1]           Print formatted versions of given URLs to stdout
  -dump-charset         Codepage to use with -dump
  -dump-color-mode      Color mode used with -dump
  -dump-width           Width of document formatted with -dump
  -eval                 Evaluate configuration file directive
  -force-html           Interpret documents of unknown types as HTML
  -?, -h, -help         Print usage help and exit
  -localhost [0|1]      Only permit local connections
  -long-help            Print detailed usage help and exit
  -lookup               Look up specified host
  -no-connect [0|1]     Run as separate instance
  -no-home [0|1]        Disable use of files in ~/.elinks
  -no-numbering         Disable link numbering in dump output
  -no-references        Disable printing of link references in dump output
  -remote               Control an already running ELinks
  -session-ring <num>   Connect to session ring with given ID
  -source [0|1]         Print the source of given URLs to stdout
  -touch-files [0|1]    Touch files in ~/.elinks when running with -no-connect/-session-ring
  -verbose <num>        Verbose level
  -version              Print version information and exit
</pre>


Now lets test our webserver with elinks, using "localhost" as our website's url:

<pre>
$ elinks http://localhost
</pre>


You might get the following:



<a href="http://codingbee.net/wp-content/uploads/2015/06/UGRNA8K.png"><img src="https://codingbee.net/wp-content/uploads/2015/06/UGRNA8K.png" alt="" width="777" height="618" class="alignnone size-full wp-image-4662" /></a>






This could be caused by the fact that the httpd service might not be running. So do ctrl+c to exit. 


Now let's check the status of the httpd service:



<pre>
$  systemctl status httpd
httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled)
   Active: inactive (dead)
</pre>

Now let's start this service (enable it so that it start on next boot):


<pre>
$ systemctl enable httpd
ln -s '/usr/lib/systemd/system/httpd.service' '/etc/systemd/system/multi-user.target.wants/httpd.service'
$ systemctl start httpd
</pre>

Now let's recheck the status:


<pre>
$ systemctl status httpd -l
httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled)
   Active: active (running) since Wed 2015-06-10 21:24:16 BST; 2s ago
  Process: 32080 ExecStop=/bin/kill -WINCH ${MAINPID} (code=exited, status=0/SUCCESS)
 Main PID: 32085 (httpd)
   Status: "Processing requests..."
   CGroup: /system.slice/httpd.service
           ├─32085 /usr/sbin/httpd -DFOREGROUND
           ├─32086 /usr/sbin/httpd -DFOREGROUND
           ├─32087 /usr/sbin/httpd -DFOREGROUND
           ├─32088 /usr/sbin/httpd -DFOREGROUND
           ├─32089 /usr/sbin/httpd -DFOREGROUND
           └─32090 /usr/sbin/httpd -DFOREGROUND

Jun 10 21:24:15 centos7.codingbee.dyndns.org systemd[1]: Starting The Apache HTTP Server...
Jun 10 21:24:16 centos7.codingbee.dyndns.org systemd[1]: Started The Apache HTTP Server.

</pre>

Now let's try elinks again, this time we should get:


<a href="http://codingbee.net/wp-content/uploads/2015/06/OFkuAXY.png"><img src="https://codingbee.net/wp-content/uploads/2015/06/OFkuAXY.png" alt="" width="1009" height="614" class="alignnone size-full wp-image-4663" /></a>


Success!



Note: Also a few other troubleshooting tips is to stop the firewalld service and restart the network service.  

Now let's write our "hello world" index.html file:


<pre>
$ echo "hello world" > /var/www/html/index.html
</pre>


Then if we check our homepage again. we should now see:


<a href="http://codingbee.net/wp-content/uploads/2015/06/PzIhkOq.png"><img src="https://codingbee.net/wp-content/uploads/2015/06/PzIhkOq.png" alt="" width="493" height="612" class="alignnone size-full wp-image-4666" /></a>


Success!


Another thing, earlier we installed the httpd-manual rpm. Let's see what config file came with this package:


<pre>
$ rpm -qc httpd-manual
/etc/httpd/conf.d/manual.conf
</pre> 

Now let's view this file:


<pre>
$ cat /etc/httpd/conf.d/manual.conf
#
# This configuration file allows the manual to be accessed at
# http://localhost/manual/
#
AliasMatch ^/manual(?:/(?:de|en|fr|ja|ko|ru))?(/.*)?$ "/usr/share/httpd/manual$1"

<Directory "/usr/share/httpd/manual">
    Options Indexes
    AllowOverride None
    Require all granted
</Directory>
</pre>

As you can see, "http://localhost/manual/" has a separate folder containing html websites. This means if we do:


<pre>
http://localhost/manual/
</pre>
 

Then web content is served from /usr/share/httpd/manual, rather than the default /var/www/html. Hence we get the following when we open this url in elinks:


<a href="http://codingbee.net/wp-content/uploads/2015/06/zFUpvbj.png"><img src="https://codingbee.net/wp-content/uploads/2015/06/zFUpvbj.png" alt="" width="1002" height="500" class="alignnone size-full wp-image-4668" /></a>