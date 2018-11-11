---
ID: 421
post_title: Viewing SELinux Contexts
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-viewing-selinux-contexts
published: true
post_date: 2015-06-18 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is the command to view user account SELinux context for the user account 'david'?"]
$ su - david
$ id 
[/toggle]
[toggle title="What is the command to view the SELinux context of the file /tmp/testfile.txt?"]
$ ls -lZ /tmp/testfile.txt
[/toggle]
[toggle title="What is the command to view all your processes along with their security contexts?"]
$ ps -efZ
[/toggle]
[toggle title="What is the command to view the security contexts for all your ports?"]
$ netstat -Z
[/toggle]
[toggle title="What are each security attribute called in the colon delimited string?"]
user:role:type:level
[/toggle]
[/accordion]
<hr/>


A lot of commands have an option "-Z" that that is specifically for displaying an object's security context. Here's a quick overview of the main ones.


<h2>Viewing a user's SELinux context</h2>
This is done using the id command: 


<pre>
[root@localhost ~]# id
uid=0(root) gid=0(root) groups=0(root) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
[root@localhost ~]# id -Z
unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
</pre>




<h2>Viewing a file's/folder's security context</h2>
As shown earlier this is done using the ls command:

<pre>
$ touch testfile.txt
$ ls -lZ testfile.txt
-rw-r--r--. root root unconfined_u:object_r:admin_home_t:s0 testfile.txt
</pre>


<h2>Viewing a process's security context</h2>
A processes SELinux context can be viewed using the ps command:


<pre>
$ ps -Z
LABEL                             PID TTY          TIME CMD
unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 1662 pts/0 00:00:00 bash
unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 2059 pts/0 00:00:00 systemctl
unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 2060 pts/0 00:00:00 less
unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 2352 pts/0 00:00:00 ps

</pre>


as well as the netstat command:


<pre>
$ netstat -Z | head
Active Internet connections (w/o servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name     Security Context               
tcp        0     64 192.168.1.124:ssh       PowershellPC:23838      ESTABLISHED 1658/sshd: root@pts  fined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
Active UNIX domain sockets (w/o servers)
Proto RefCnt Flags       Type       State         I-Node   PID/Program name     Security Context                                  Path
unix  2      [ ]         DGRAM                    10539    1/systemd            system_u:system_r:init_t:s0                        /run/systemd/shutdownd
unix  5      [ ]         DGRAM                    6267     1/systemd            system_u:system_r:init_t:s0                        /run/systemd/journal/socket
.
.
...etc

</pre>


Notice in all cases, they show all 4 parts, using ":" as a delimiter, and they show all the parts in the order of: <code>user:role:type:level</code>.

You'll notice that the user security contect attribute, always ends with "_u" suffix, and in a similar fashion for role we have (_r), and for type we have (_t). However for the level have a prefix of "s", which indicates (s)ensitivity.