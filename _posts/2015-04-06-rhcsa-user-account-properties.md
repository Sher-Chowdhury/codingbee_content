---
ID: 345
post_title: 'RHCSA &#8211; User Account properties'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-user-account-properties
published: true
post_date: 2015-04-06 00:00:00
---
Before you create a new user account, there's a few user account properties that you need to know:


<ul>
	<li>usernma</li>
	<li>password - in /etc/shodow, if it shows "!!" then it means that account doesn't have a password yet. You fix this by running the "passwd username" command as the root user</li>
	<li>uid</li>
	<li>guid - full list of guids are available in /etc/groups file</li>
	<li>home directory</li>
	<li>group name - a primary group is created at the same time as the user account, and it is named after the user account's username</li>
	<li>GECOS - a description for the user. </li>
	<li>home directory</li>
	<li>default shell</li>
	<li></li>
</ul>