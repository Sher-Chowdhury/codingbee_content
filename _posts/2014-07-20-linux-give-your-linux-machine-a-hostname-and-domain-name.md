---
ID: 129
post_title: 'Linux &#8211; Give your Linux machine a hostname and domain-name'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/uncategorized/linux-give-your-linux-machine-a-hostname-and-domain-name
published: true
post_date: 2014-07-20 00:00:00
---
When you build a brand new linux machine, you machine will not have a name. As a result, your router (i.e. network) will instead refer to your machine by it's mac-address:

<a href="http://codingbee.net/wp-content/uploads/2014/07/2hGFpvg.png"><img class="alignnone size-full wp-image-1231" src="http://codingbee.net/wp-content/uploads/2014/07/2hGFpvg.png" alt="" width="343" height="344" /></a>

Now you can assign a name for your hostname using the hostname command, like this:

[bash]
[root@localhost ~]# hostname PuppetMaster.MayfieldRoad.net
[root@localhost ~]# hostname
PuppetMaster
[/bash]

Here, I've called my machine "PuppetMaster", which is the hostname. I also set the domain name as "MayfieldRoad.net",

which you can confirm like this:

[bash]
[sher@PuppetMaster ~]$ hostname --domain
MayfieldRoad.net
[/bash]

Note, the above won't work, unless you have set "MayfieldRoad.net" as your domain on your router. You can set the domain up in your router, by logging into your router's web console, and change the dhcp setting, e.g.:

<a href="http://codingbee.net/wp-content/uploads/2014/07/Ra2mb9x.png"><img class="alignnone size-full wp-image-1239" src="http://codingbee.net/wp-content/uploads/2014/07/Ra2mb9x.png" alt="" width="1079" height="620" /></a>

to check if that is already done, simply open up a browser, and go to:

RT-N66U.MayfieldRoad.net

Note, in this case the "RT-N66U" is the model name of my Asus router. You can also confirm this by telneting into the router, using putty and the router's ip. and then viewing the /etc/hosts files.

Unfortunately the setting the hostname (and domain) using the "hostname" command won't persist after a reboot, to make it permanent, you need to use the "nmtui" bios-like-gui command instead:

<a href="http://codingbee.net/wp-content/uploads/2014/07/dZ1wLte.png"><img class="alignnone size-full wp-image-1240" src="http://codingbee.net/wp-content/uploads/2014/07/dZ1wLte.png" alt="" width="350" height="327" /></a>

Note: the nmtui command is new addition to centos/rhel 7. For older versions you need to edit the following file:

<em>/etc/sysconfig/network</em>

Note: If the machine you are working on is a vm clone, then the <a title="VirtualBox - cloning RHEL/Cento checklist" href="http://codingbee.net/tutorials/virtualbox/virtualbox-cloning-rhelcento-checklist/">mac address is hardcoded in the config files</a>, which needs to be fixed.

&nbsp;

After that you can check this has been set by doing:

[sher@PuppetMaster ~]$ hostnamectl status
Static hostname: PuppetMaster.mayfieldroad.net
Icon name: computer
Chassis: n/a
Machine ID: 7adea129229342bb9f7735b5dfd42050
Boot ID: db90bac6e96148bfac35b43ca5ccde59
Operating System: CentOS Linux 7 (Core)
CPE OS Name: cpe:/o:centos:centos:7
Kernel: Linux 3.10.0-123.4.2.el7.x86_64
Architecture: x86_64

Another better check is trying to see if you can connect to the machine using it's hostname.domainname rather than it's IP address.

Reference:
http://www.itzgeek.com/how-tos/linux/centos-how-tos/change-hostname-in-centos-7-rhel-7.html#axzz381VBEDUs