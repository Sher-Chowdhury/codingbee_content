---
ID: 422
post_title: 'RHCSA &#8211; The &#8220;user&#8221;, &#8220;role&#8221; and &#8220;level&#8221; security attributes'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-the-user-role-and-level-security-attributes
published: true
post_date: 2015-06-18 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is the command to list all the available 'user' security attribute values?"]
$ seinfo <span style='letter-spacing:1.5px'>-</span>-user
[/toggle]
[toggle title="What is the command to show the logic that SELinux uses to determine which of these values should be assigned for a newly created user?"]
$  semanage login -l
[/toggle]
[toggle title="What is the command to list all the available 'role' security attribute values?"]
$   seinfo <span style='letter-spacing:1.5px'>-</span>-role
[/toggle]
[/accordion]

<hr/>


As mentioned before, the "targeted" policy primarily decides on granting access of one object to another object, based on the "type" security attributes, i.e it is a Type Enforcement based policy.   


That means that that none of the other security attributes values matters that much.   


<h2>The "user" security attribute</h2> 
Eerlier we saw that are 4000+ possible values that a "type" security value can have, since that is the main one. In contrast there are less than 10 possible values that a "user" selinux attribute can have:


<pre>
$ seinfo -u

Users: 8
   sysadm_u
   system_u
   xguest_u
   root
   guest_u
   staff_u
   user_u
   unconfined_u
</pre> 


However the targeted policy still has to assign a user security context value when a user is created. Here are the policy rules in the targeted policy,  to help it determine what user security attribute value is assigned to a newly created user.   


<pre>
$  semanage login -l

Login Name           SELinux User         MLS/MCS Range        Service

__default__          unconfined_u         s0-s0:c0.c1023       *
root                 unconfined_u         s0-s0:c0.c1023       *
system_u             system_u             s0-s0:c0.c1023       *

</pre>


Notice that system_u is for any service accounts that are created during an rpm package install. These service accounts will trigger processes, and these processes will inherit the initiating user's security attribute values, which explains this:


<pre>
<span style='font-size:10px'>$ ps -efZ | grep "httpd"
system_u:system_r:httpd_t:s0    root      1304     1  0 07:20 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
system_u:system_r:httpd_t:s0    apache    1407  1304  0 07:20 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
system_u:system_r:httpd_t:s0    apache    1408  1304  0 07:20 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
system_u:system_r:httpd_t:s0    apache    1409  1304  0 07:20 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
system_u:system_r:httpd_t:s0    apache    1410  1304  0 07:20 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
system_u:system_r:httpd_t:s0    apache    1411  1304  0 07:20 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
</span>
</pre>

Inheriting security attribute values is the default unless the targeted policy specifies otherwise. 


Also notice that the default "user" security attribute value for creating a new user is "unconfined_u". You can modify this table so that when you create a user account with a certain name, then it adopts a different "user" security attribute value. Not sure how to do this yet.  


 
<h2>The "role" security attribute</h2> 
The role security attribute is used for using Roles Based Access Control (RBAC). The targeted policy works by stating with users can access which roles, and in turn, which roles can access which domains.  


<pre>
$ seinfo -r

Roles: 14
   auditadm_r
   dbadm_r
   guest_r
   staff_r
   user_r
   logadm_r
   object_r
   secadm_r
   sysadm_r
<strong>   system_r</strong>
   webadm_r
   xguest_r
   nx_server_r
   unconfined_r
</pre>


Note, "system_r" is usually reserved for system processes. The same goes for system_u, which we saw earlier. They usually both go hand-in-hand. 

roles are hardly used. 



<h2>The "level" security attribute</h2> 
This is something that is exclusive to the MLS policy type is hardly ever used. It is something that you don't need to know about for the red hat exams.