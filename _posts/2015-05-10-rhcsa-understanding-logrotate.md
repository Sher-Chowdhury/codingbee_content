---
ID: 390
post_title: 'RHCSA &#8211; understanding Logrotate'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-understanding-logrotate
published: true
post_date: 2015-05-10 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is the main logrotate system's config file?"]
/etc/logrotate.conf
[/toggle]
[toggle title="Where can rpm packages drop in custom logrotate.conf config files?"]
/etc/logrotate.d
[/toggle]
[toggle title="True or false, custom logrotate files overides the default settings specified in the main logrotate file?"]
True
[/toggle]
[toggle title="Where can you find help info for logrotate?"]
$ man logrotate
[/toggle]
[toggle title="What is the command to check the logrotate's service status?"]
Trick question, logrotate doesn't actually have a service.
[/toggle]
[toggle title="Where is the logrotate's cron job located?"]
/etc/cron.daily/logrotate
[/toggle]
[/accordion]

<hr/>



Over time, log files such as /var/log/messages can get so big that they exceed linux's max file size limit, or they use up all the available disk space. To prevent the above scenarios from happening, we using a system called Logrotate. 


The way it works is that after a certain period of time, log rotate renames and archives the existing log file, and then create a brand new empty log file to take it's place. Log rotate also retains the x most recent logs, and deletes the rest. 

The advantage of this is that logrotate ensures that the disk space doesn't all get used up. However the compromise is that we lose the full historical log data over time, which is a problem in the very small chance if we want to view them. To overcome this, you can set up a remote "log server", which all rotated logs are sent to, whicl 


To configure logratate, we have the following:


<pre>
$ ls -l /etc | grep logrotate
-rw-r--r--.  1 root root      662 Jul 31  2013 logrotate.conf
drwxr-xr-x.  2 root root     4096 Apr  3 16:39 logrotate.d
</pre>

The logrotate.d directory is used by other rpm packages to drop in it's own custom logrotate config files into. Hence in the logrotate.d directory we have:

<pre>
$ ls -l /etc/logrotate.d
total 56
-rw-r--r--. 1 root root 178 Jun 18  2014 chrony
-rw-r--r--. 1 root root  71 Mar  5 22:19 cups
-rw-r--r--. 1 root root 541 Sep 19  2014 glusterfs
-rw-r--r--. 1 root root 172 Mar  6 03:08 iscsiuiolog
-rw-r--r--. 1 root root 165 Mar 26 23:20 libvirtd
-rw-r--r--. 1 root root 163 Mar 26 23:20 libvirtd.qemu
-rw-r--r--. 1 root root 106 Mar  6 02:18 numad
-rw-r--r--. 1 root root 136 Jun 10  2014 ppp
-rw-r--r--. 1 root root 408 Mar  6 05:05 psacct
-rw-r--r--. 1 root root 115 Mar 13 22:35 samba
-rw-r--r--. 1 root root 219 Mar 26 13:23 sssd
-rw-r--r--. 1 root root 210 Oct 13  2014 syslog
-rw-r--r--. 1 root root 100 Dec  4 13:18 wpa_supplicant
-rw-r--r--. 1 root root 100 Mar  9 20:39 yum
</pre>


Any configurations in the logrotate.d directory will override the default settings which are set in the main <code>logrotate.conf</code> settings. 

Now let's take a look at the logrotate.conf file:


<pre>
cat /etc/logrotate.conf
# see "man logrotate" for details
# rotate log files weekly
weekly

# keep 4 weeks worth of backlogs
rotate 4

# create new (empty) log files after rotating old ones
create

# use date as a suffix of the rotated file
dateext

# uncomment this if you want your log files compressed
#compress

# RPM packages drop log rotation information into this directory
include /etc/logrotate.d

# no packages own wtmp and btmp -- we'll rotate them here
/var/log/wtmp {
    monthly
    create 0664 root utmp
        minsize 1M
    rotate 1
}

/var/log/btmp {
    missingok
    monthly
    create 0600 root utmp
    rotate 1
}

# system-specific logs may be also be configured here.


</pre>

Now let's take a look at package specific logrotate config files:


<pre>
 cat /etc/logrotate.d/yum
/var/log/yum.log {
    missingok
    notifempty
    size 30k
    yearly
    create 0600 root root
}

</pre>

You can find what each of the above means by checking out the logrotate's man pages:

<pre>
$ man logrotate
</pre>

logrotate doesn't function as a service, instead it runs in the form of a daily cron job:

<pre>
$ pwd
/etc/cron.daily
$ ls -l | grep logrotate
-rwx------. 1 root root 180 Jul 31  2013 logrotate
</pre>

Note, becuase of this, you can't rotate logs less than a day, e.g. every hour, which wouldn't be good practice anyway. 


See also:

http://www.thegeekstuff.com/2010/07/logrotate-examples/


<a href="http://www.thegeekstuff.com/2012/01/rsyslog-remote-logging/">http://www.thegeekstuff.com/2012/01/rsyslog-remote-logging/</a> (this actually to do with setting up a log server, which you need know for rhce)