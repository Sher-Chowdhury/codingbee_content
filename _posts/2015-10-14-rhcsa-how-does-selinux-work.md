---
ID: 469
post_title: 'RHCSA &#8211; How does SELinux work?'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-how-does-selinux-work
published: true
post_date: 2015-10-14 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What are objectives in the world of SELinux?"]
Apply a SELinux label to absolutely everything, e.g. files, folders, processes, user accounts...etc. 
[/toggle]
[toggle title="What are the SELinux labels called?"]
- SELinux contexts   
# or
- Security contexts
[/toggle]
[toggle title="What is the command to displays the SELinux contexts of the file /tmp/testfile.txt?"]
$ ls -lZ testfile.txt
[/toggle]
[toggle title="What is the format of an SELinux context?"]
An SELinux context comprised of 4 colon delimited strings. Each string is referred to as a "security attribute"
[/toggle]
[toggle title="What gives SELinux Contexts meaning?"]
The Policy Type
[/toggle]
[toggle title="What is a policy type?"]
You can think of it as really big text book, and this book contains an entry for each security attribute, followed by a list of other security attributes that it is allowed to access.
[/toggle]
[toggle title="What is each entry in the policy type referred to as?"]
- policy rule    
# or simply: 
- policy
[/toggle]
[toggle title="What is the command to view 'allow' policy rules for the security attribute, 'httpd_content_type'?"]
$ sesearch <span style="letter-spacing:1.5px">-</span>-allow | grep httpd_content_type
# this outputs:
allow httpd_t httpd_content_type : file { ioctl read getattr lock open } ;
allow httpd_t httpd_content_type : dir { getattr search open } ;
allow httpd_suexec_t httpd_content_type : dir { getattr search open } ;
allow httpd_t httpd_content_type : file { ioctl read getattr lock open } ;
allow httpd_t httpd_content_type : dir { ioctl read getattr lock search open } ;
[/toggle]
[toggle title="What does the first line of the output means?"]
Allow an object of the type 'httpd_t' to access other objects of the type 'httpd_content_type', as long as these objects are 'file' objects. Furthermore they are allowed to have { ioctl read getattr lock open } types of access. 
[/toggle]
[/accordion]

<hr/>


SELinux operates at the kernel level. This means that when one objects (e.g. a process) attempts to access another object (e.g. config file), the kernel checks the relevant "policy" for the given objects to see if the access should be permitted, the kernel then either grants or deny access. 

The heart of SELinux comprises of 2 parts:

<ul>
	<li>Security Contexts</li>
	<li>Policy Type</li>
</ul>



<h2>Security Contexts</h2>
SELinux assigns a label to every single "object" on your machine. An "object" can be absolutely anything, including:

<ul>
	<li>user accounts</li>
	<li>user groups</li>
	<li>files</li>
	<li>folders</li>
	<li>processes</li>
</ul>



These labels are referred to as <strong>SELinux Contexts</strong>, aka <strong>security contexts</strong>. 

A lot of commands have a "-Z" option for displaying security contexts. For example the ls command has a -Z option:


<pre>
$ touch testfile.txt
$ ls -lZ testfile.txt
-rw-r--r--. root root <strong>unconfined_u:object_r:admin_home_t:s0</strong> testfile.txt
</pre> 

Notice that SELinux automatically assigns a security context to newly created objects.  

As you can see each SELinux context is made up of (colon delimited parts) 4 parts, which in this case are:

<ul>
	<li>unconfined_u</li>
	<li>object_r</li>
	<li>admin_home_t</li>
	<li>s0</li>
</ul>


These items are referred to as "Security Attributes". We will cover more about <a href="http://codingbee.net/tutorials/rhcsa/rhcsa-selinux-security-contexts-aka-labels/">Security contexts and Security attributes</a> later. 

These Security Contexts on their own are useless, and it is the "policy types" that gives them meaning.


<h2>Policy Type</h2>
You can think of a policy type as really big text book, and this book contains an entry for each security attribute, followed by a list of other security attributes that it is permitted to access. 

<pre>
$ sesearch --all | wc -l
ERROR: Cannot get avrules: Neverallow rules requested but not available
70620
[root@puppetmaster ~]# sesearch --all | head
ERROR: Cannot get avrules: Neverallow rules requested but not available

Found 17669 semantic te rules:
   type_transition sosreport_t sssd_initrc_exec_t : process initrc_t;
   type_transition certmonger_unconfined_t sssd_initrc_exec_t : process initrc_t;
   type_transition piranha_pulse_t prelude_audisp_exec_t : process prelude_audisp_t;
   type_transition authconfig_t NetworkManager_initrc_exec_t : process initrc_t;
   type_transition dbadm_sudo_t sudo_exec_t : process dbadm_t;
   type_transition anaconda_t sssd_initrc_exec_t : process initrc_t;
   type_transition ricci_modservice_t ddclient_initrc_exec_t : process initrc_t;
   type_transition kdumpctl_t lsassd_exec_t : process lsassd_t;

</pre>

<provide an example>


Each entry in the policy type is referred to as a <strong>policy rule</strong>, or just <strong>policy</strong>. You can modify these mapping at a higher level by configuring <a href="http://codingbee.net/tutorials/rhcsa/rhcsa-selinux-booleans/">sebooleans</a>, we'll cover this later.

There's also 3 different policy types to choose from, (i.e. three different books). We'll cover these 3 <a href="http://codingbee.net/tutorials/rhcsa/rhcsa-selinux-policy-types-and-selinux-attributes/">policy types</a> later. There are several types of of policy rules, which you learn about via sesearch's man page. One of the type is the "allow" policy rule.      


Here are several allow policy rules relating to httpd_content_type:

<pre>
$ sesearch --allow | grep httpd_content_type
<strong>   allow httpd_t httpd_content_type : file { ioctl read getattr lock open } ;
</strong>   allow httpd_t httpd_content_type : dir { getattr search open } ;
   allow httpd_suexec_t httpd_content_type : dir { getattr search open } ;
   allow httpd_t httpd_content_type : file { ioctl read getattr lock open } ;
   allow httpd_t httpd_content_type : dir { ioctl read getattr lock search open } ;
   allow httpd_t httpd_content_type : lnk_file { read getattr } ;

</pre>

Let's break down the very first rules in this list. This rule says that an object of the type <strong>httpd_t</strong> is allowed to access an object of the type <strong>httpd_content_type</strong>, as long as that object is a <strong>file</strong>. Further more it can only have <strong>{ ioctl read getattr lock open }</strong> types of access.

All "allow" policy rules are structured like this.