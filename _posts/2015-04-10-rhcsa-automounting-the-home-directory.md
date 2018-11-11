---
ID: 355
post_title: 'RHCSA &#8211; Automounting the home directory'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-automounting-the-home-directory
published: true
post_date: 2015-04-10 00:00:00
---
https://linuxacademy.com/cp/livelabs/view/id/61

need to write how to use the 

* and "&" notation as well as the extra ldap related mounting options. 


I.e. the auto.home will look something like this:


<pre>$ cat /etc/auto.home
*     -rw        nfsserver:/home/guests/&
</pre>

This special shorthand notation will save the need for writing hundreds of lines, one for each user's home directory. 

Here the "*" is a wildcard which points to any directory (which matches the user's username)

The "&" automatically gets resolved to the matching the username. This shorthand is specifically designed to help with automounting a user's home directory. 

Also I think for security reasons, the * and & are tied into the user's username. So that the user can't wonder into someone else's home directory.  

So when a user logs in, their cwd will automatically get directed to their home directory which will then trigger the automount. 


This solution makes it possible for user to have access to their personal files irrespective of which machine the log into, as long as the machine has home directory automounting configured. 


This special syntax structure is documented in the autofs man page. 


<pre>
$ man 5 autofs
</pre>