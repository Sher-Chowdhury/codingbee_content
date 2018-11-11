---
ID: 3105
post_title: >
  Postfix – Set up a Postfix Null Client
  on RHEL/CentOS 7
author: sher
post_excerpt: "Here's a step-by-step walkthrough on how to setup a Null client using Postfix. "
layout: post
permalink: >
  https://codingbee.net/tutorials/rhce/postfix-set-up-a-postfix-null-client-on-rhel-centos-7
published: true
post_date: 2018-03-19 14:33:59
---
A <a href="http://www.postfix.org/STANDARD_CONFIGURATION_README.html">null client</a> (aka a forwarding agent): 

<ul>
	<li>Is a machine that has a mail server software running on it - you can install a mail client, e.g. Thunderbird so to create mails for the mail server to send.</li>
	<li>Can only send mail. It can't even send mail locally</li>
	<li>Doesn't try to work out how to best route the email to it's destination. In fact, it is quite crude and sends all mails to a central mail server, which in turn does the actual routing work.</li>
</ul> 

You can follow along this article by using our <a href="https://github.com/Sher-Chowdhury/CentOS7-Postfix-demo">CentOS 7 postfix vagrant demo project</a>. In our example we have the following setup:

<pre>
+--------------------+    +--------------------------+    +--------------------+
|                    |    |                          |    |                    |
| null-client.cb.net |    |central-mail-server.cb.net|    | mail-client.cb.net |
|                    |    |                          |    |                    |
|                    |    |                          |    |                    |
|                    |--->|                          |--->|                    |
|   mta: postfix     |    |     mta: postfix         |    |   mta: postfix     |
|   mua: mailx       |    |                          |    |   mua: mailx       |
|                    |    |                          |    |                    |
|   sender: jerry    |    |                          |    |   receiver: tom    |
+--------------------+    +--------------------------+    +--------------------+
</pre>




Some terminalogies. 


<strong>Mail User Agent (MUA)</strong> - this is an app that's used for creating emails. E.g. mail, mailx, outlook, mozilla thunderbird,...etc. You can install an MUA on your Null client to create and send emails. However these emails must be outbound, i.e. you can't write emails to be sent locally to local user account on the Null Client. 

<strong>Mail Transport Agent (MTA)</strong> - This term is used to refer to 'sending mail servers' and 'receiving mail servers'. They both use the SMTP protocol, which runs on port 25. Hence a Null Client is considered as an MTA.  


This article covers how to setup the null client, which in our example is called null-client.cb.net. Also in our example we will confirm that we'll validate everything is setup correctly by sending an email as user jerry, to the user tom (who resides on mail-client.cb.net). We'll assume that the central email server and 


The first step to setup a Null client is to install the postfix rpm:

<pre>
$ yum install postfix
</pre>


We now need to make changes to <code>/etc/postfix/main.cf</code> which is the main Postfix config file. First we'll take create a backup:

<pre>
$ cp /etc/postfix/main.cf /etc/postfix/main.cf-orig
</pre>


To edit this file, you can either use vim, or do it via the postconf utility. In our case we'll use postconf to set the following:


<pre>
$ postconf -e myhostname=$(hostname)
$ postconf -e mydomain=cb.net
$ postconf -e local_transport='error: this is a null client'
$ postconf -e myorigin='$myhostname'
$ postconf -e inet_interfaces=loopback-only
$ postconf -e mydestination=''           # this is intentionally left blank
$ postconf -e mynetworks='127.0.0.0/8 [::1]/128'
$ postconf -e relayhost=[central-mail-server.cb.net]
</pre>

To find out what these settings mean, see:

<pre>
$ man 5 postconf
</pre>

We also need to set the following in order to get Null client working in our Vagrant environment:

<pre>
$ postconf -e disable_dns_lookups=yes
</pre>


To summarise, the main configurations that needs modifying are:


<strong>myhostname</strong>: The  internet  hostname  of this mail system.

<strong>mydomain</strong>: The internet domain name of this mail system. 


<strong>relayhost</strong>: The next-hop destination of non-local mail; overrides non-local domains in recipient  addresses.. 


<strong>inet_interfaces</strong>: This set the interfaces that our mail server is allowed to receive mails from. A null client shouldn't be receiving any mails so we restrict this to only the loopback interface. This means that when we start the postfix daemon, it only listens on the lo interface. 

<strong>mynetworks</strong>: this refers to the network of the inet_interface. 

<strong>myorigin</strong>: This lets you specify what domain you want locally-originating email to appear to come from. In otherwords it let's you customise the "From" section in your email. In big companies, the use the domain name rather than hostname, e.g. they use username@gmail.com, rather than something like username@box4343@gmail.com. 

<strong>mydestination</strong>: This specifies what hostnames/domain that this server should interpret as being the local destination for. For a null client this needs to be set to blank, in order to prevent the mail server from sending any mail to any local accounts. 


<strong>local_transport</strong>: This specifies how emails destined to $mydestination should be delivered. 

Once you have set these values, you can perform a syntax check:


<pre>
$ postfix check
</pre>

For troubleshooting purposes, you can also compare the existing default value against the new value like this:

<pre>
$ postconf -d inet_interfaces
inet_interfaces = all

$ postconf -n inet_interfaces
inet_interfaces = loopback-only
</pre>


Finally we enable and restart postfix:


<pre>
$ systemctl restart postfix
$ systemctl enable postfix
</pre>

Now we are ready to start testing. To do this, lets first install an MUA, in our case we'll use mailx:

<pre>
$ yum install mailx
</pre>

Next we create our user called jerry, and we switch to that user:

<pre>
[root@null-client postfix]# useradd jerry
[root@null-client postfix]# su - jerry
[jerry@null-client ~]$
</pre>

Next we send a test email:

<pre>
$ echo "Hello Matt, this is Jerry" | mail -s "test email" matt@mail-client.cb.net
</pre>


After sending a test mail, use the following to check if it has left your box:

<pre>
[jerry@null-client ~]$ mailq
Mail queue is empty
</pre>


Now if we log into the mail client, and check if we got any mail:


<pre>
[root@mail-client ~]# su - matt
[matt@mail-client ~]$ mail
Heirloom Mail version 12.5 7/5/10.  Type ? for help.
"/var/spool/mail/matt": 2 messages 2 new
>N  1 root                  Fri Apr 13 17:39  24/1045  "test email"
 N  2 jerry@null-client.cb  Fri Apr 13 23:08  24/1045  "test email"
& 2
Message  2:
From jerry@null-client.cb.net  Fri Apr 13 23:08:38 2018
Return-Path: <jerry@null-client.cb.net>
X-Original-To: matt@mail-client.cb.net
Delivered-To: matt@mail-client.cb.net
Date: Fri, 13 Apr 2018 23:08:38 +0000
To: matt@mail-client.cb.net
Subject: test email
User-Agent: Heirloom mailx 12.5 7/5/10
Content-Type: text/plain; charset=us-ascii
From: jerry@null-client.cb.net
Status: R

Hello Matt, this is Jerry

</pre>


https://www.cyberciti.biz/tips/howto-postfix-flush-mail-queue.html

https://app.pluralsight.com/library/courses/linux-email-servers-lpic-2/table-of-contents

https://app.pluralsight.com/player?course=linux-networking-service-management-security-fundamentals&author=andrew-mallett&name=linux-networking-service-management-security-fundamentals-m6&clip=0&mode=live


[post-content post_name=rhsca-quiz] 

[accordion]
[toggle title="What are the main steps to setting up the null client?"]
01. install rpms
02. edit the /etc/postfix/main.cf
03. sanity check the updated configs
04. start the postfix service
05. test with mailx 
[/toggle]

[toggle title="what is the command to install the rpms for setting up a null client?"]
$ yum install postfix
$ yum install mailx
[/toggle]
[toggle title="What is the main postfix config file that needs changing?"]
/etc/postfix/main.cf
[/toggle]
[toggle title="What is the command to display list all postfix effective settings?"]
$ postconf
[/toggle]
[toggle title="What is the command to display a full list of implicit default postfix configuration values?"]
$ postconf -d
[/toggle]
[toggle title="What is a handy grep command to list most of the postconf setting of interest?"]
$ postconf | grep '^my'
[/toggle]
[toggle title="What are the commands to run to make the changes?"]
$ postconf -e myhostname=$(hostname)
$ postconf -e mydomain=cb.net
$ postconf -e myorigin='$myhostname'
$ postconf -e mynetworks='127.0.0.0/8 [::1]/128'
$ postconf -e mydestination=''           # this is intentionally left blank
$ postconf -e local_transport='error: this is a null client'
$ postconf -e inet_interfaces=loopback-only
$ postconf -e relayhost=[central-mail-server.cb.net]
# for the vagrant environment, we also need to set:
$ postconf -e disable_dns_lookups=yes
[/toggle]
[toggle title="What is the command to display a full list of explicitly postfix configuration values?"]
$ postconf -n 
[/toggle]
[toggle title="Where can you find more info about these settings?"]
$ man 5 postconf
[/toggle]
[toggle title="What is the command to do a syntax check of the postfix values?"]
$ postfix check
[/toggle]
[toggle title="What is the command to restart the postfix daemon?"]
$ systemctl restart postfix
$ systemctl enable postfix
[/toggle]
[toggle title="What is the command to send a test mail to matt@mail-client.cb.net?"]
echo "hello tom, this is jerry" | mail -s "test email" matt@mail-client.cb.net
[/toggle]
[/accordion]