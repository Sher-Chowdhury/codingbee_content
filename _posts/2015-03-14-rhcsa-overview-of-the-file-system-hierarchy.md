---
ID: 323
post_title: 'RHCSA &#8211; Overview of the file system hierarchy'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-overview-of-the-file-system-hierarchy
published: true
post_date: 2015-03-14 00:00:00
---
<h2>System Hierarchy overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is the command the displays help info about your machine's high level folder structures?"]
$ man hier
[/toggle]
[/accordion]

<hr/>

<h2>System Hierarchy</h2>
At it's most fundamental level, Everything in Linux is a file. 

The top most folder in linux is called the root directory. This folder contains all the other folders and files. In Linux the root directory and is represent by "/". So to navigate to the root folder we do:


<pre>
$ cd /
</pre>

This contains the following:

<pre>
$ ls -l
total 98
dr-xr-xr-x.  2 root root  4096 Mar  9 09:33 bin
dr-xr-xr-x.  5 root root  1024 Oct 15 13:14 boot
drwxr-xr-x  18 root root  3480 Mar 13 10:49 dev
drwxr-xr-x. 98 root root  4096 Mar 13 10:49 etc
drwxr-xr-x.  3 root root  4096 Oct 15 23:54 home
dr-xr-xr-x. 11 root root  4096 Dec 24 15:51 lib
dr-xr-xr-x.  9 root root 12288 Mar  9 09:33 lib64
drwx------.  2 root root 16384 Oct 15 13:03 lost+found
drwxr-xr-x.  2 root root  4096 Sep 23  2011 media
drwxr-xr-x.  2 root root  4096 Sep 23  2011 mnt
drwxr-xr-x.  4 root root  4096 Oct 15 23:55 opt
dr-xr-xr-x  98 root root     0 Mar 13 10:49 proc
dr-xr-x---.  3 root root  4096 Dec 24 16:16 root
dr-xr-xr-x.  2 root root 12288 Mar  8 12:07 sbin
drwxr-xr-x.  2 root root  4096 Oct 15 13:03 selinux
drwxr-xr-x.  2 root root  4096 Sep 23  2011 srv
drwxr-xr-x  13 root root     0 Mar 13 10:49 sys
drwxrwxrwt.  7 root root  4096 Mar 14 16:19 tmp
drwxr-xr-x. 13 root root  4096 Oct 15 13:05 usr
drwxr-xr-x   2 root root  4096 Dec 24 15:39 vagrant
drwxr-xr-x. 21 root root  4096 Dec 24 15:51 var
</pre>
 
To get a brief idea of what are stored in each of these folders, you can view the <code> hier</code> man pages:

<pre>
$ man hier
</pre>