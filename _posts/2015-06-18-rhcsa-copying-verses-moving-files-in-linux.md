---
ID: 424
post_title: 'SELinux &#8211; Copying verses moving files'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-copying-verses-moving-files-in-linux
published: true
post_date: 2015-06-18 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="True or False, security contexts stays intact when you move a file from one folder to another?"]
True, but they get reassigned with new SELinux context when you copy files instead of moving.
[/toggle]
[toggle title="What is better, moving or copying files, in order to avoid getting SELinux related errors?"]
Copying files is the better approach. 
[/toggle]
[toggle title="What is the command to check for files with possible incompatible SELinux contects?"]
$ sealert -a /var/log/audit/audit.log
[/toggle]
[toggle title="What is the command to reset the SELinux context (according to the rules defined in the targeted policy) for the file /var/html/hello.html?"]
$ restorecon /var/html/hello.html
[/toggle]
[/accordion]

<hr/>

In this article we are going to take a look at files ending up with the wrong SELinux context, how they ended up with the wrong SELinux context, what are the consequences of having the wrong SELinux context, and what could be done to fix (or avoid) this problem. Files usually end up with the wrong SELinux context when you move a file, instead of copying it. This article will walk you through a typical example. First let's confirm that we are in enforcing mode:

<pre>
$ getenforce
Enforcing
</pre>


Let's say we have set up httpd, we only did a simple install and started the http service:


<pre>
$ yum install httpd 
$ systemctl enable httpd.service
$ systemctl start httpd.service
</pre>


Now let's see if we have a file called hello.html:


<a href="http://codingbee.net/wp-content/uploads/2015/11/918GwO4.png"><img src="https://codingbee.net/wp-content/uploads/2015/11/918GwO4.png" alt="" width="690" height="561" class="alignnone size-full wp-image-5897" /></a>


As you can see this doesn't exist yet. Now let's create this file:

<pre>
$ echo "this is a web page" > /var/www/html/hello.html
</pre>

and then refresh the page:

<a href="http://codingbee.net/wp-content/uploads/2015/11/dZTAbMl.png"><img src="https://codingbee.net/wp-content/uploads/2015/11/dZTAbMl.png" alt="" width="700" height="572" class="alignnone size-full wp-image-5899" /></a>

So far so good. Now we are going  delete this file and recreate it again but this time in a slightly different way, in order to illustrate how SELinux could come into play. So let's first delete this file:

<pre>
$ rm /var/www/html/hello.html
</pre>

Now lets create hello.html again but this time in the root user's home directory:


<pre>
$ pwd
/root
$ echo "this is a web page" > hello.html
$ ls -lZ hello.html
-rw-r--r--. root root unconfined_u:object_r:admin_home_t:s0 hello.html
</pre>

As you can see the hello.html file has inherited it's parent folder's security attribute, in particular the value of the  "type" security attribute: "admin_home_t". 

Now let's move this file into /var/www/html:

<pre>
$ mv /root/hello.html /var/www/html/
$ ls -lZ /var/www/html/hello.html
-rw-r--r--. root root unconfined_u:object_r:admin_home_t:s0 /var/www/html/hello.html

</pre>
Notice that this file has retained the original SELinux context, (which happens to be wrong for files that are in this directory).

Now let's refresh the page:

<a href="http://codingbee.net/wp-content/uploads/2015/11/sQcvbm9.png"><img src="https://codingbee.net/wp-content/uploads/2015/11/sQcvbm9.png" alt="" width="695" height="570" class="alignnone size-full wp-image-5900" /></a>

Now we get a forbidden message. That's because the file has a security context, that the httpd.service isn't allowed to access. 

There's 2 ways to fix this. First we could have used the cp command instead of the mv command. Or we could reset the hello.html file's security to what it should be according to the targeted policy, which we do using the <code>restorecon</code> command. But first let's check the logs to confirm what we are suspecting:


  
<pre>
$ sealert -a /var/log/audit/audit.log
 99% done'list' object has no attribute 'split'
100% done
found 1 alerts in /var/log/audit/audit.log
--------------------------------------------------------------------------------

SELinux is preventing /usr/sbin/httpd from getattr access on the file /var/www/html/hello.html.

*****  Plugin restorecon (99.5 confidence) suggests   ************************

<strong>If you want to fix the label.
/var/www/html/hello.html default label should be httpd_sys_content_t.
Then you can run restorecon.
Do
# /sbin/restorecon -v /var/www/html/hello.html
</strong>
*****  Plugin catchall (1.49 confidence) suggests   **************************

If you believe that httpd should be allowed getattr access on the hello.html file by default.
Then you should report this as a bug.
You can generate a local policy module to allow this access.
Do
allow this access for now by executing:
# grep httpd /var/log/audit/audit.log | audit2allow -M mypol
# semodule -i mypol.pp


Additional Information:
Source Context                system_u:system_r:httpd_t:s0
Target Context                unconfined_u:object_r:admin_home_t:s0
Target Objects                /var/www/html/hello.html [ file ]
Source                        httpd
Source Path                   /usr/sbin/httpd
Port                          <Unknown>
Host                          <Unknown>
Source RPM Packages           httpd-2.4.6-31.el7.centos.1.x86_64
Target RPM Packages
Policy RPM                    selinux-policy-3.13.1-23.el7_1.18.noarch
Selinux Enabled               True
Policy Type                   targeted
Enforcing Mode                Enforcing
Host Name                     puppetagent01.local
Platform                      Linux puppetagent01.local
                              3.10.0-229.14.1.el7.x86_64 #1 SMP Tue Sep 15
                              15:05:51 UTC 2015 x86_64 x86_64
Alert Count                   5
First Seen                    2015-11-09 12:06:51 GMT
Last Seen                     2015-11-09 12:15:19 GMT
Local ID                      5192d41a-a235-4eb3-b31f-e4d4c0d1e8b2

Raw Audit Messages
type=AVC msg=audit(1447071319.198:784): avc:  denied  { getattr } for  pid=4389 comm="httpd" path="/var/www/html/hello.html" dev="dm-0" ino=136577068 scontext=system_u:system_r:httpd_t:s0 tcontext=unconfined_u:object_r:admin_home_t:s0 tclass=file


type=SYSCALL msg=audit(1447071319.198:784): arch=x86_64 syscall=stat success=no exit=EACCES a0=7f65eb34fd18 a1=7ffe1285c500 a2=7ffe1285c500 a3=7f65df9f7792 items=0 ppid=4388 pid=4389 auid=4294967295 uid=48 gid=48 euid=48 suid=48 fsuid=48 egid=48 sgid=48 fsgid=48 tty=(none) ses=4294967295 comm=httpd exe=/usr/sbin/httpd subj=system_u:system_r:httpd_t:s0 key=(null)

Hash: httpd,httpd_t,admin_home_t,file,getattr

</pre>

So now let's run this fix:

<pre>
$ ls -lZ /var/www/html/hello.html
-rw-r--r--. root root unconfined_u:object_r:admin_home_t:s0 /var/www/html/hello.html
$ restorecon /var/www/html/hello.html
$ ls -lZ /var/www/html/hello.html
-rw-r--r--. root root unconfined_u:object_r:httpd_sys_content_t:s0 /var/www/html/hello.html
</pre>
 


<a href="http://codingbee.net/wp-content/uploads/2015/11/dZTAbMl.png"><img src="https://codingbee.net/wp-content/uploads/2015/11/dZTAbMl.png" alt="" width="700" height="572" class="alignnone size-full wp-image-5899" /></a>

Success!

Now we could have avoided this problem to begin with by creating the hello.html file in the correct folder to begin with. Or failing that, we could have used the cp command instead of mv command. i.e.:


<pre>
$ echo "this is a web page" > hello.html
$ ls -lZ /root/hello.html
-rw-r--r--. root root unconfined_u:object_r:admin_home_t:s0 /root/hello.html
$ cp /root/hello.html /var/www/html/hello.html
$ ls -lZ /var/www/html/hello.html
-rw-r--r--. root root unconfined_u:object_r:httpd_sys_content_t:s0 /var/www/html/hello.html
</pre>