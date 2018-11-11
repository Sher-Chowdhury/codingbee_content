---
ID: 388
post_title: 'Systemd &#8211; Using Journald on CentOS/RHEL 7'
author: sher
post_excerpt: >
  Journald is a service that systemd uses
  for capturing logs
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-using-journald-on-centos-rhel-7
published: true
post_date: 2018-03-29 00:00:00
---
Journald is a service that systemd uses for capturing logs:

<pre>
$ systemctl status systemd-journald
systemd-journald.service - Journal Service
   Loaded: loaded (/usr/lib/systemd/system/systemd-journald.service; static)
   Active: active (running) since Sat 2015-09-26 16:35:29 BST; 42min ago
     Docs: man:systemd-journald.service(8)
           man:journald.conf(5)
 Main PID: 468 (systemd-journal)
   Status: "Processing requests..."
   CGroup: /system.slice/systemd-journald.service
           └─468 /usr/lib/systemd/systemd-journald

Sep 26 16:35:29 puppetmaster.local systemd-journal[468]: Runtime journal is using 6.2M (max 49.6M,...).
Sep 26 16:35:29 puppetmaster.local systemd-journal[468]: Runtime journal is using 6.2M (max 49.6M,...).
Sep 26 16:35:29 puppetmaster.local systemd-journal[468]: Journal started
Sep 26 16:35:38 puppetmaster.local systemd-journal[468]: Runtime journal is using 6.2M (max 49.6M,...).
Hint: Some lines were ellipsized, use -l to show in full.

</pre>


Journald logs everything that it receives from systemd. journald stores all it's log entries into a binary file. Hence you can't view this file directly. Instead you need to use the <code>journalctl</code> command. Running journalctl on it's own gives you absolutely everything:


<pre>
$ journalctl
-- Logs begin at Sun 2015-05-10 09:40:20 BST, end at Sun 2015-05-10 11:09:18 BST. --
May 10 09:40:20 localhost.localdomain systemd-journal[90]: Runtime journal is using 6.2M (max 49.6M, leaving 74.5M of free 490.5M, c
May 10 09:40:20 localhost.localdomain systemd-journal[90]: Runtime journal is using 6.2M (max 49.6M, leaving 74.5M of free 490.5M, c
.
.
...etc

</pre>
 
Journald even includes all the log entries that are captured by rsyslog (i.e. everyting in /var/log/mesages). That's because rsyslog runs as a service which means it's managed by systemd, and therefore systemd is fully aware of everything that rsyslog logs and passes that to journald. Therefore journald essentially aggregates all the logs and stores them in a single log. You can view the last few lines like this:


<pre>
$ journalctl -xn
</pre>

Here we have chosen also to view additional e(x)planation for some log entries. The 'n' means display the last few (n)umber of lines, which defaults to 10. 

It is best practice to always use the "x" flag when using journalctl. You can view log entries that are (p)riority-labelled as "info":

<pre>
journalctl -xp info
</pre>

You can view all the available priority labels in the journalctl man pages. You can also (f)ollow (like 'tail -f') journald logs like this:

<pre>
$ journalctl -fx
</pre>

Another thing you will want to do is view the logs entries for a given (u)nit, e.g. the sshd.service:


<pre>
$ journalctl -xu sshd
-- Logs begin at Sun 2015-05-10 09:40:20 BST, end at Sun 2015-05-10 11:19:01 BST. --
May 10 09:40:44 localhost.localdomain systemd[1]: Starting OpenSSH server daemon...
May 10 09:40:44 localhost.localdomain systemd[1]: Started OpenSSH server daemon.
May 10 09:40:44 localhost.localdomain sshd[1333]: Server listening on 0.0.0.0 port 22.
May 10 09:40:44 localhost.localdomain sshd[1333]: Server listening on :: port 22.
May 10 09:41:39 localhost.localdomain sshd[1777]: Accepted password for root from 192.168.1.244 port 7768 ssh2
</pre>

This gives all log entries relating to the sshd.service only. Therefore a really useful troubleshooting technique would be something like:

<pre>
$ journalctl -fxu httpd.service
</pre>

This will tail journald for any log entries relating to the httpd service, and provide any explanations where available (which are indicated by the "--" prefix). Here's a sample output of this:

<pre>
$ journalctl -fxu httpd.service
-- Logs begin at Sat 2015-09-26 16:35:25 BST. --
Sep 26 16:35:38 puppetmaster.local systemd[1]: Starting The Apache HTTP Server...
-- Subject: Unit httpd.service has begun with start-up
-- Defined-By: systemd
-- Support: http://lists.freedesktop.org/mailman/listinfo/systemd-devel
--
-- Unit httpd.service has begun starting up.
Sep 26 16:35:41 puppetmaster.local systemd[1]: Started The Apache HTTP Server.
-- Subject: Unit httpd.service has finished start-up
-- Defined-By: systemd
-- Support: http://lists.freedesktop.org/mailman/listinfo/systemd-devel
--
-- Unit httpd.service has finished starting up.
--
-- The start-up result is done.
Sep 26 16:35:52 puppetmaster.local puppet-master[2983]: Starting Puppet master version 3.8.2
Sep 26 16:36:19 puppetmaster.local puppet-master[3298]: Compiled catalog for puppetmaster.local in...ds
Sep 26 17:05:51 puppetmaster.local puppet-master[3298]: Compiled catalog for puppetmaster.local in...ds
Sep 26 17:35:50 puppetmaster.local puppet-master[3298]: Compiled catalog for puppetmaster.local in...ds
Sep 26 18:05:50 puppetmaster.local puppet-master[3298]: Compiled catalog for puppetmaster.local in...ds

</pre>


You can filter this further using the priority, the best one for troubleshooting purposes is "debug":

<pre>
$ journalctl -fxu sshd.service -p debug
</pre>



<h2>Configuring Journald</h2>
Journald can be configured by editing <code>/etc/systemd/journald.conf</code>. 


<pre>
$ cat /etc/systemd/journald.conf
#  This file is part of systemd.
#
#  systemd is free software; you can redistribute it and/or modify it
#  under the terms of the GNU Lesser General Public License as published by
#  the Free Software Foundation; either version 2.1 of the License, or
#  (at your option) any later version.
#
# Entries in this file show the compile time defaults.
# You can change settings by editing this file.
# Defaults can be restored by simply deleting this file.
#
# See journald.conf(5) for details.

[Journal]
#Storage=auto
#Compress=yes
#Seal=yes
#SplitMode=uid
#SyncIntervalSec=5m
#RateLimitInterval=30s
#RateLimitBurst=1000
#SystemMaxUse=
#SystemKeepFree=
#SystemMaxFileSize=
#RuntimeMaxUse=
#RuntimeKeepFree=
#RuntimeMaxFileSize=
#MaxRetentionSec=
#MaxFileSec=1month
#ForwardToSyslog=yes
#ForwardToKMsg=no
#ForwardToConsole=no
#ForwardToWall=yes
#TTYPath=/dev/console
#MaxLevelStore=debug
#MaxLevelSyslog=debug
#MaxLevelKMsg=notice
#MaxLevelConsole=info
#MaxLevelWall=emerg
</pre>


For example, by default journald wipes out all logs during a shutdown. If you want journald logs entries to be persistant then you just need to set 'Storage' to 'persistent'. 





[post-content post_name=rhsca-quiz] 

[accordion]
[toggle title="What is the command to check the journald's service status?"]
$ systemctl status systemd-journald
[/toggle]
[toggle title="Which 2 commands can be used to view journald log entries?"]
- journalctl
- systemctl
[/toggle]
[toggle title="What is the command to view just a the last few lines (without tailing it)?"]
$ journalctl -xn          # provide extra e(x)planation and last 10 li(n)es
[/toggle]
[toggle title="What is the command to just view all the 'info' labelled journald log entries?"]
$ journalctl -xp info     # filter for (p)riority 
[/toggle]
[toggle title="What is the command to tail journald logs?"]
$ journalctl -xf          # (f)ollow the log
[/toggle]
[toggle title="What is the command to just view journald logs relating to the sshd service?"]
$ journalctl -xu sshd     # filter for (u)nit
[/toggle]
[toggle title="What is the command to just tail in realtie, journald logs relating to the httpd service?"]
$ journalctl -fxu httpd.service   
[/toggle]
[toggle title="What is the command to just view journald logs relating to the httpd service, and only dislplay log entries that are labelled 'debug'?"]
$ journalctl -fxu sshd.service -p debug
[/toggle]
[toggle title="What is journald's main config file?"]
/etc/systemd/journald.conf
[/toggle]
[/accordion]