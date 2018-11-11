---
ID: 428
post_title: 'SELinux &#8211; SELinux extended man pages'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-selinux-extended-man-pages
published: true
post_date: 2015-06-21 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is the command to load in SELinux specific man pages?"]
$ sepolicy manpage -a -p /usr/share/man/man8
[/toggle]
[toggle title="What is the command you then need to run?"]
$ mandb
[/toggle]
[toggle title="What is the command to view all the newly available man pages?"]
$ man -K "_selinux"
[/toggle]
[/accordion]

<hr/>


So far we saw that the (target) policy dictates what security label each and every object should have, and it stores all the policy rules, i.e. which objects can access other objects based on the security labels. 


Another thing you might want to do is find out what a security label actually means. There are lots of man pages that hold this info. These man pages aren't loaded in by default and to load them in, you need to use the <strong>sepolicy</strong>. 

<pre>
$ whatis sepolicy
sepolicy (8)         - SELinux Policy Inspection tool
</pre>


First we run the following command (need to memorize this):


<pre>
$ sepolicy manpage -a -p /usr/share/man/man8
</pre>


Then run mandb to update the man pages and add in the new pages:

<pre>
$ mandb
</pre>

Finally you can view all the newly added SELinux man pages like this:


<pre>
<span style='font-size:11px'>$ man -k "_selinux" 
abrt_dump_oops_selinux (8) - Security Enhanced Linux Policy for the abrt_dump_oops processes
abrt_handle_event_selinux (8) - Security Enhanced Linux Policy for the abrt_handle_event processes
abrt_helper_selinux (8) - Security Enhanced Linux Policy for the abrt_helper processes
abrt_retrace_coredump_selinux (8) - Security Enhanced Linux Policy for the abrt_retrace_coredump processes
abrt_retrace_worker_selinux (8) - Security Enhanced Linux Policy for the abrt_retrace_worker processes
abrt_selinux (8)     - Security Enhanced Linux Policy for the abrt processes
.
.
.
...etc.</span>
</pre> 

There is quite a lot!

These man pages are quite comprehensive detailed info about various security contexts and booleans  are available here.