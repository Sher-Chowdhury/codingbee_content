---
ID: 423
post_title: 'SELinux &#8211; Booleans'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-selinux-booleans
published: true
post_date: 2015-06-18 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is the command to view the man info for SElinux booleans?"]
$ man booleans
[/toggle]
[toggle title="What is the command to view all the current seboolan settings?"]
$ getsebool -a 
[/toggle]
[toggle title="What is the command to view a short description for each boolean setting?"]
$ semanage boolean -l
[/toggle]
[toggle title="What is the command to persistantly+verbosely enable the 'ftp_home_dir' SELinux boolean setting?"]
$ setsebool -PV ftp_home_dir on  
# this takes about 20 seconds
[/toggle]
[/accordion]

<hr/>



You can customise the default behaviour of your (targeted) policy. This is done by configuring various boolean settings that can enable/disable your policy's various policy rules. Going back to our massive textbook analogy, this is a bit like graying-out (or ungraying) various segements of the book. Switching boolean values can be like increasing/decreasing the set of security attributes that a particular security attribute is allowed to have access to.  

All seboolean changes takes effect straight away. Here's a man page that covers more about SELinux booleans:

<pre>
$ man booleans
</pre> 

You can view all your policy's boolean setting using the <code>getboolean</code> command:


<pre>
$ getsebool -a 
abrt_anon_write --> off
abrt_handle_event --> off
abrt_upload_watch_anon_write --> on
antivirus_can_scan_system --> off
antivirus_use_jit --> off
auditadm_exec_content --> on
.
.
...etc.
</pre>

New boolean settings also gets added to this list when in install rpm packages, e.g. httpd. You can find a short description of each seboolean using <code>semanage</code>:



<pre>
$ semanage boolean -l
SELinux boolean        State  Default Description

ftp_home_dir           (off  ,  off)  Determine whether ftpd can read and write files in user home directories.
smartmon_3ware         (off  ,  off)  Determine whether smartmon can support devices on 3ware controllers.
mpd_enable_homedirs    (off  ,  off)  Determine whether mpd can traverse user home directories.
xdm_sysadm_login       (off  ,  off)  Allow the graphical login program to login directly as sysadm_r:sysadm_t
.
.
...etc
</pre>

Notice we have a current "state" column, as well as an on machine reboot "default" column. 


<h2>Enabling and Disabling SELinux Booleans</h2>

To enable/disable SELinux Boolean settings, we need to use the <code>setsebool</code> command:

<pre>
$ whatis setsebool
setsebool (8)        - set SELinux boolean value
</pre> 

E.g. the "ftp_home_dir" is currently disabled, and to enable it we do:


<pre>
$ getsebool ftp_home_dir
ftp_home_dir --> off
$ setsebool -P ftp_home_dir on
$ getsebool ftp_home_dir
ftp_home_dir --> on
</pre>

<strong>Important:</strong> Always use the "-P" to make it persistent. 

The above command for enabling/disabling a boolean setting takes about 20 seconds. That's because the target policy itself is being modified and new binary of the policy is being compiled. The new binary is located in the folder <code>/etc/selinux/targeted/policy</code>