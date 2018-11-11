---
ID: 429
post_title: SELinux Log Files
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-selinux-log-files
published: true
post_date: 2015-06-21 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is the command to check the status of the service that logs all SELinux related log entries?"]
$ systemctl status auditd
[/toggle]
[toggle title="Which file stores all SELinux related log entries?"]
/var/log/audit/audit.log
[/toggle]
[toggle title="What is the (cat/grep) command that filters the above file to only display SELinux related log entries?"]
$ cat /var/log/audit/audit.log | grep "AVC"
[/toggle]
[toggle title="What is a better alternative to the above command?"]
$ sealert -a /var/log/audit/audit.log
[/toggle]
[toggle title="Where can you find additional SELinux related log entries?"]
$ vim /var/log/messages  # search for 'sealert'
[/toggle]
[/accordion]

<hr/>



When you are having SELinux access problems one of the first things to investigate are the log files. The auditd service is responsible for writing SELinux logs, so first we need to make sure it is running:

<pre>$ systemctl status auditd
auditd.service - Security Auditing Service
   Loaded: loaded (/usr/lib/systemd/system/auditd.service; enabled)
   Active: active (running) since Sat 2015-06-20 07:20:26 BST; 1 day 3h ago
 Main PID: 581 (auditd)
   CGroup: /system.slice/auditd.service
           ├─581 /sbin/auditd -n
           ├─587 /sbin/audispd
           └─589 /usr/sbin/sedispatch
</pre>


This service will write all SELinux related access denied messages to the <code>/var/log/audit/audit.log</code> file. However this file records lots of other things and not just SELinux related log entries. Therefore we grep for "AVC" to view just SELinux related log entries:

<pre>
$ cat /var/log/audit/audit.log | grep "AVC"
type=AVC msg=audit(1434247738.386:471): avc:  denied  { getattr } for  pid=1435 comm="httpd" path="/var/www/html/index.html" dev="dm-1" ino=71561345 scontext=system_u:system_r:httpd_t:s0 tcontext=unconfined_u:object_r:admin_home_t:s0 tclass=file
type=AVC msg=audit(1434247738.386:472): avc:  denied  { getattr } for  pid=1435 comm="httpd" path="/var/www/html/index.html" dev="dm-1" ino=71561345 scontext=system_u:system_r:httpd_t:s0 tcontext=unconfined_u:object_r:admin_home_t:s0 tclass=file
type=USER_AVC msg=audit(1434247801.772:476): pid=1 uid=0 auid=4294967295 ses=4294967295 subj=system_u:system_r:init_t:s0 msg='avc:  received setenforce notice (enforcing=1)  exe="/usr/lib/systemd/systemd" sauid=0 hostname=? addr=? terminal=?'
.
.
</pre>

This works because all SELinux log entries contains the string "AVC".  

However you can also parse this log file into the <strong>sealert</strong> utility which will then output the relevant parts of this log in a more human readable format:


<pre>
$ sealert -a /var/log/audit/audit.log
</pre>


If after viewing the logs, you are still unsure how to fix this, then you could also looking at the main log file, <code>/var/log/messages</code>, using the "less" command. In main log file, try searching for the keyword <code>sealert</code>. You should then find an entry in the log file that looks something like this:


<pre>
Jun 21 12:14:20 localhost setroubleshoot: SELinux is preventing /usr/sbin/httpd from read access on the file testfile.html. For complete SELinux messages. run sealert -l d72b5c45-af2d-4a29-95cd-8a7289594b85
Jun 21 12:14:20 localhost python: SELinux is preventing /usr/sbin/httpd from read access on the file testfile.html.

</pre>

This should hopefully give enough for you to determine what's wrong. You can also run the sealert command as suggested. So if we run it:


<pre>
 sealert -l d8dfacad-00f5-4796-a00d-c02bbfdc3a41
SELinux is preventing /usr/sbin/httpd from getattr access on the file /var/www/html/testfile.html.

*****  Plugin restorecon (<strong>99.5 confidence</strong>) suggests   ************************

If you want to fix the label.
/var/www/html/testfile.html default label should be httpd_sys_content_t.
Then you can run restorecon.
Do
<strong># /sbin/restorecon -v /var/www/html/testfile.html</strong>

*****  Plugin catchall (1.49 confidence) suggests   **************************

If you believe that httpd should be allowed getattr access on the testfile.html file by default.
Then you should report this as a bug.
You can generate a local policy module to allow this access.
Do
allow this access for now by executing:
# grep httpd /var/log/audit/audit.log | audit2allow -M mypol
# semodule -i mypol.pp


Additional Information:
Source Context                system_u:system_r:httpd_t:s0
Target Context                unconfined_u:object_r:admin_home_t:s0
Target Objects                /var/www/html/testfile.html [ file ]
Source                        httpd
Source Path                   /usr/sbin/httpd
Port                          <Unknown>
Host                          localhost.localdomain
Source RPM Packages           httpd-2.4.6-31.el7.centos.x86_64
Target RPM Packages
Policy RPM                    selinux-policy-3.13.1-23.el7_1.7.noarch
Selinux Enabled               True
Policy Type                   targeted
Enforcing Mode                Enforcing
Host Name                     localhost.localdomain
Platform                      Linux localhost.localdomain
                              3.10.0-229.4.2.el7.x86_64 #1 SMP Wed May 13
                              10:06:09 UTC 2015 x86_64 x86_64
Alert Count                   2
First Seen                    2015-06-21 12:14:16 BST
Last Seen                     2015-06-21 12:45:01 BST
Local ID                      d8dfacad-00f5-4796-a00d-c02bbfdc3a41

Raw Audit Messages
type=AVC msg=audit(1434887101.673:2151): avc:  denied  { getattr } for  pid=15563 comm="httpd" path="/var/www/html/testfile.html" dev="dm-1" ino=69211150 scontext=system_u:system_r:httpd_t:s0 tcontext=unconfined_u:object_r:admin_home_t:s0 tclass=file


type=SYSCALL msg=audit(1434887101.673:2151): arch=x86_64 syscall=lstat success=no exit=EACCES a0=7f61bd817c68 a1=7fff6a1c4ec0 a2=7fff6a1c4ec0 a3=0 items=0 ppid=1304 pid=15563 auid=4294967295 uid=48 gid=48 euid=48 suid=48 fsuid=48 egid=48 sgid=48 fsgid=48 tty=(none) ses=4294967295 comm=httpd exe=/usr/sbin/httpd subj=system_u:system_r:httpd_t:s0 key=(null)

Hash: httpd,httpd_t,admin_home_t,file,getattr


</pre>


sealert gives a lot of useful info, and it even suggests what command it thinks you need to run that will resolve this issue (with 99.5%) confidence. However as indicated by the confidence level, it's suggestion might not always be correct.