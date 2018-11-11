---
ID: 462
post_title: 'RHCSA &#8211; List of all resources that needs to be persistant'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-list-of-all-resources-that-needs-to-be-persistant
published: true
post_date: 2015-10-04 00:00:00
---
There are some changes that you can make to a machine that get's reverted back to what the were before. 

It is really important to know what you need to do so that these changes can survive a reboot, i.e. make them persistant.

For  

<ul>
	<li><a href="http://codingbee.net/tutorials/rhcsa/rhcsa-network-interface-card-nic/">network interfaces (done using nmcli)</a></li>
	<li>systemd units </li>
	<li>default target</li>
	<li>Setting the default kernel version</li>
	<li></li>
</ul>


kvm vms
network interfaces (done using nmcli)
filesystem mounts
Selinux stuff