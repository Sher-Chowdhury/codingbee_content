---
ID: 389
post_title: 'RHCSA &#8211; Rsyslog logging'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-rsyslog-logging
published: true
post_date: 2015-05-10 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is the command, to check the logging service's status which is used by  httpd, yum, ssh,...etc?"]
$ systemctl status rsyslog
[/toggle]
[toggle title="What are the 8 log priority labels?"]
- debug
- info
- notice
- warn (or warning)
- err
- crit
- alert
- emerg
[/toggle]
[toggle title="What is the main config for rsyslog?"]
/etc/rsyslog.conf
[/toggle]
[toggle title="What is the command to view the main log file in realtime?"]
$ tail -F /var/log/messages
# or 
$ tailf /var/log/messages
[/toggle]
[toggle title="What is the command to add 'hello world' to to /var/log/messages?"]
$ logger "hello world"
[/toggle]

[/accordion]

<hr/>




<h2>Intro</h2>

Nearly all services (e.g. httpd, vsftpd, and yum) records messages in their various log files. However they don’t write these logs entries directly to the associated log files (i.e. they don't do “echo “xxx” >>  log-file.txt”). Instead, these services send their log entries to an intermediary service called rsyslog (short for “remote system logger”). rsyslog then writes the log entries to the appropriate log file.

rsyslog actually runs as a service:


<pre>
$ systemctl status rsyslog
rsyslog.service - System Logging Service
   Loaded: loaded (/usr/lib/systemd/system/rsyslog.service; enabled)
   Active: active (running) since Sun 2015-05-10 09:40:34 BST; 2h 6min ago
 Main PID: 638 (rsyslogd)
   CGroup: /system.slice/rsyslog.service
           └─638 /usr/sbin/rsyslogd -n

May 10 09:40:34 localhost.localdomain systemd[1]: Started System Logging Service.


</pre>

For rsyslog to work, each message is sent to rsyslog with a priority label. Here are the <a href="http://linux.die.net/man/5/rsyslog.conf">priority levels</a>:
<ol>
	<li>debug</li>
	<li>info</li>
	<li>notice</li>
	<li>warn (or warning)</li>
	<li>err</li>
	<li>crit</li>
	<li>alert</li>
	<li>emerg</li>
</ol>

This comes in useful when we come to look at the rsyslog's main config file, which is:


<pre>
$ cat /etc/rsyslog.conf
# rsyslog configuration file

# For more information see /usr/share/doc/rsyslog-*/rsyslog_conf.html
# If you experience problems, see http://www.rsyslog.com/doc/troubleshoot.html

#### MODULES ####

# The imjournal module bellow is now used as a message source instead of imuxsock.
$ModLoad imuxsock # provides support for local system logging (e.g. via logger command)
$ModLoad imjournal # provides access to the systemd journal
#$ModLoad imklog # reads kernel messages (the same are read from journald)
#$ModLoad immark  # provides --MARK-- message capability

# Provides UDP syslog reception
#$ModLoad imudp
#$UDPServerRun 514

# Provides TCP syslog reception
#$ModLoad imtcp
#$InputTCPServerRun 514


#### GLOBAL DIRECTIVES ####

# Where to place auxiliary files
$WorkDirectory /var/lib/rsyslog

# Use default timestamp format
$ActionFileDefaultTemplate RSYSLOG_TraditionalFileFormat

# File syncing capability is disabled by default. This feature is usually not required,
# not useful and an extreme performance hit
#$ActionFileEnableSync on

# Include all config files in /etc/rsyslog.d/
$IncludeConfig /etc/rsyslog.d/*.conf                 # this is important. 

# Turn off message reception via local log socket;
# local messages are retrieved through imjournal now.
$OmitLocalLogging on

# File to store the position in the journal
$IMJournalStateFile imjournal.state


#### RULES ####

# Log all kernel messages to the console.
# Logging much else clutters up the screen.
#kern.*                                                 /dev/console

# Log anything (except mail) of level info or higher.
# Don't log private authentication messages!
*.info;mail.none;authpriv.none;cron.none                /var/log/messages

# The authpriv file has restricted access.
authpriv.*                                              /var/log/secure

# Log all the mail messages in one place.
mail.*                                                  -/var/log/maillog


# Log cron stuff
cron.*                                                  /var/log/cron

# Everybody gets emergency messages
*.emerg                                                 :omusrmsg:*

# Save news errors of level crit and higher in a special file.
uucp,news.crit                                          /var/log/spooler

# Save boot messages also to boot.log
local7.*                                                /var/log/boot.log


# ### begin forwarding rule ###
# The statement between the begin ... end define a SINGLE forwarding
# rule. They belong together, do NOT split them. If you create multiple
# forwarding rules, duplicate the whole block!
# Remote Logging (we use TCP for reliable delivery)
#
# An on-disk queue is created for this action. If the remote host is
# down, messages are spooled to disk and sent when it is up again.
#$ActionQueueFileName fwdRule1 # unique name prefix for spool files
#$ActionQueueMaxDiskSpace 1g   # 1gb space limit (use as much as possible)
#$ActionQueueSaveOnShutdown on # save messages to disk on shutdown
#$ActionQueueType LinkedList   # run asynchronously
#$ActionResumeRetryCount -1    # infinite retries if host is down
# remote host is: name/ip:port, e.g. 192.168.0.1:514, port optional
#*.* @@remote-host:514
# ### end of the forwarding rule ###


</pre>


Let's take a look at parts of this file, first we have:


<pre>
.
.
# Include all config files in /etc/rsyslog.d/
$IncludeConfig /etc/rsyslog.d/*.conf             
.
.
</pre>

This rsyslog.d directory that can hold other rsyslog related config files:


<pre>
$ pwd
/etc/rsyslog.d
$ ls -l
total 8
-rw-r--r--. 1 root root 2564 Sep 19  2014 gluster.conf.example
-rw-r--r--. 1 root root   49 Mar 26 13:03 listen.conf

</pre>


Next we have the following really important section:



<pre>
.
.
# Log anything (except mail) of level info or higher.
# Don't log private authentication messages!
*.info;mail.none;authpriv.none;cron.none                /var/log/messages
.
.
</pre>

In plain English, the above line says: log ALL messages of priority "info" and above (*.info), but filter out all "mail" related messages (mail.none), and all "news" related   messages (news.none), and all authentication messages (authpriv.none), and all "cron" messages (cron.none). As you can see, nearly all messages gets sent to <code>/var/log/messages</code>. 

The messages that have been filtered out, are actually logged elsewhere, as indicated by the following extract:

<pre>
.
.
# The authpriv file has restricted access.
authpriv.*                                              /var/log/secure

# Log all the mail messages in one place.
mail.*                                                  -/var/log/maillog


# Log cron stuff
cron.*                                                  /var/log/cron
.
.
</pre>

The "-" in -/var/log/maillog means that the log entries can temporarily reside in a buffer if necessary.

Finally we have:


<pre>
# Everybody gets emergency messages
*.emerg                                                 :omusrmsg:*
</pre>

Here a special "omusrmsg" plugin is being used so that any emergency message is appended to ALL log files. 
 

One final thing to remember, if you do make any changes to the above config files, then you need to restart the rsyslog service for the changes to take into effect:


<pre>
 systemctl status rsyslog
rsyslog.service - System Logging Service
   Loaded: loaded (/usr/lib/systemd/system/rsyslog.service; enabled)
   Active: active (running) since Sun 2015-05-10 09:40:34 BST; 4h 43min ago
 Main PID: 638 (rsyslogd)
   CGroup: /system.slice/rsyslog.service
           └─638 /usr/sbin/rsyslogd -n

May 10 09:40:34 localhost.localdomain systemd[1]: Started System Logging Service.
</pre>



You can monitor log entries in realtime using the tail command (with the follow flag):


<pre>$ tail -F /var/log/messages</pre>

Or you could do:

<pre>$ tailf /var/log/messages</pre>

Another option you can do is:
<pre>
$ vim /var/log/messages
</pre>
The benefit of this approach is that vim will intelligently use syntax highlighting to highlight error messages. 


If you have multiple log files, then you can view them all in real time like this:



<pre>
$ tail -F /var/log/httpd/*.log
==> /var/log/httpd/default_error.log <==

==> /var/log/httpd/foreman_access.log <==
127.0.0.1 - - [19/Sep/2015:21:05:02 +0100] "HEAD / HTTP/1.1" 301 - "-" "-"
127.0.0.1 - - [19/Sep/2015:21:05:01 +0100] "HEAD / HTTP/1.1" 301 - "-" "-"
127.0.0.1 - - [25/Sep/2015:22:03:40 +0100] "HEAD / HTTP/1.1" 500 - "-" "-"
127.0.0.1 - - [25/Sep/2015:22:07:12 +0100] "HEAD / HTTP/1.1" 301 - "-" "-"
127.0.0.1 - - [26/Sep/2015:08:17:29 +0100] "HEAD / HTTP/1.1" 301 - "-" "-"
127.0.0.1 - - [26/Sep/2015:08:17:30 +0100] "HEAD / HTTP/1.1" 301 - "-" "-"
127.0.0.1 - - [26/Sep/2015:08:22:10 +0100] "HEAD / HTTP/1.1" 301 - "-" "-"
127.0.0.1 - - [26/Sep/2015:08:22:10 +0100] "HEAD / HTTP/1.1" 301 - "-" "-"
127.0.0.1 - - [26/Sep/2015:16:35:43 +0100] "HEAD / HTTP/1.1" 301 - "-" "-"
127.0.0.1 - - [26/Sep/2015:16:35:43 +0100] "HEAD / HTTP/1.1" 301 - "-" "-"

==> /var/log/httpd/foreman_error.log <==

==> /var/log/httpd/foreman-ssl_access_ssl.log <==
127.0.0.1 - - [26/Sep/2015:16:36:18 +0100] "GET /node/puppetmaster.local?format=yml HTTP/1.1" 200 321 "-" "Ruby"
127.0.0.1 - - [26/Sep/2015:16:36:20 +0100] "POST /api/reports HTTP/1.1" 201 511 "-" "Ruby"
127.0.0.1 - - [26/Sep/2015:17:05:49 +0100] "GET /node/puppetmaster.local?format=yml HTTP/1.1" 200 321 "-" "Ruby"
127.0.0.1 - - [26/Sep/2015:17:05:51 +0100] "POST /api/hosts/facts HTTP/1.1" 201 773 "-" "Ruby"
127.0.0.1 - - [26/Sep/2015:17:05:51 +0100] "GET /node/puppetmaster.local?format=yml HTTP/1.1" 200 321 "-" "Ruby"
127.0.0.1 - - [26/Sep/2015:17:05:52 +0100] "POST /api/reports HTTP/1.1" 201 518 "-" "Ruby"
127.0.0.1 - - [26/Sep/2015:17:35:48 +0100] "GET /node/puppetmaster.local?format=yml HTTP/1.1" 200 321 "-" "Ruby"
127.0.0.1 - - [26/Sep/2015:17:35:50 +0100] "POST /api/hosts/facts HTTP/1.1" 201 773 "-" "Ruby"
127.0.0.1 - - [26/Sep/2015:17:35:50 +0100] "GET /node/puppetmaster.local?format=yml HTTP/1.1" 200 321 "-" "Ruby"
127.0.0.1 - - [26/Sep/2015:17:35:51 +0100] "POST /api/reports HTTP/1.1" 201 510 "-" "Ruby"

==> /var/log/httpd/foreman-ssl_error_ssl.log <==

==> /var/log/httpd/puppet_access_ssl.log <==
127.0.0.1 - - [26/Sep/2015:17:05:48 +0100] "GET /production/node/puppetmaster.local?transaction_uuid=ce413f09-b8be-4585-91f1-2395f72fa880&fail_on_404=true HTTP/1.1" 200 5010 "-" "Ruby"
127.0.0.1 - - [26/Sep/2015:17:05:50 +0100] "GET /production/file_metadatas/pluginfacts?links=manage&recurse=true&ignore=.svn&ignore=CVS&ignore=.git&checksum_type=md5 HTTP/1.1" 200 303 "-" "Ruby"
127.0.0.1 - - [26/Sep/2015:17:05:50 +0100] "GET /production/file_metadatas/plugins?links=manage&recurse=true&ignore=.svn&ignore=CVS&ignore=.git&checksum_type=md5 HTTP/1.1" 200 303 "-" "Ruby"
127.0.0.1 - - [26/Sep/2015:17:05:51 +0100] "POST /production/catalog/puppetmaster.local HTTP/1.1" 200 579 "-" "Ruby"
</pre>

Note: tailf can't handle multiple log files. 


Finally you can add your own custom log entries to /var/log/messages using the logger command. For example to add an entry with the message "hello world":

<pre>
$ logger "hello world"
</pre>

Now let's confirm that this has worked:


<pre>
$ tail -3 /var/log/messages
Nov 11 20:10:01 puppetmaster systemd: Starting Session 17 of user root.
Nov 11 20:10:01 puppetmaster systemd: Started Session 17 of user root.
Nov 11 20:13:36 puppetmaster root: hello world
</pre>


<h2>See also</h2>

<a href="http://www.thegeekstuff.com/2011/08/linux-var-log-files/" rel="nofollow">http://www.thegeekstuff.com/2011/08/linux-var-log-files/</a>