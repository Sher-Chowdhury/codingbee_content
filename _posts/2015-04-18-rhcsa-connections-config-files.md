---
ID: 362
post_title: 'RHCSA &#8211; Connections config files'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-connections-config-files
published: true
post_date: 2015-04-18 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="Which directory contains all the network connection files?"]
/etc/sysconfig/network-scripts
[/toggle]
[toggle title="What is a connections file's naming convention?"]
ifcfg-{interface-name}
[/toggle]
[toggle title="In the connections file, what setting do you need to apply to enable dhcp?"]
BOOTPROTO="dhcp"
[/toggle]
[toggle title="In the connections file, what setting do you need to apply to set an IP address?"]
IPADDR0={ip-number}

[/toggle]
[toggle title="In the connections file, what setting do you need to apply to set a Gateway?"]
GATEWAY0={ip-number}

[/toggle]
[toggle title="In the connections file, what setting do you need to apply to ensure that Network Manager doesn't manage this connection?"]
NM_CONTROLLED=no

[/toggle]
[toggle title="In the connections file, what setting do you need to apply to ensure that this connection is used when machine is started up?"]
ONBOOT='yes'

[/toggle]
[toggle title="In the connections file, what setting do you need to apply to set the netmask to 28?"]
PREFIX=28   

[/toggle]
[toggle title="In the connections file, what setting do you need to apply to set the DNS to 8.8.8.8?"]
DNS1=8.8.8.8              
[/toggle]
[toggle title="What is the command?"]


[/toggle]
[/accordion]

<hr/>



The following directory houses connection config files for every single interface. These names of these files all follow the convention <strong>ifcfg-{interface-name}</strong>, in our case conly 2 files fit that naming convention:

<pre>
$ ls -l /etc/sysconfig/network-scripts 
total 232
<strong>-rw-r--r--. 1 root root   321 Mar 14 19:23 ifcfg-enp0s3
-rw-r--r--. 1 root root   254 Jan 15 08:57 ifcfg-lo
</strong>lrwxrwxrwx. 1 root root    24 Apr  3 16:19 ifdown -> ../../../usr/sbin/ifdown
-rwxr-xr-x. 1 root root   627 Jan 15 08:57 ifdown-bnep
-rwxr-xr-x. 1 root root  5817 Jan 15 08:57 ifdown-eth
-rwxr-xr-x. 1 root root  6196 Mar  6 05:17 ifdown-ib
.
.
...etc</pre>

We can ignore the lo interface since that refers to the loopback interface, which is a software only implementation of an interface.

If we take a look at this config file, we see the following:

<pre>$ cat /etc/sysconfig/network-scripts/ifcfg-enp0s3
HWADDR="08:00:27:82:83:FC"    # this is the interfaces mac address
TYPE="Ethernet"         # this means it is wired connection, as opposed to wifi 
BOOTPROTO="dhcp"           # this means we are using dhcp
DEFROUTE="yes"              # ignore this 
PEERDNS="yes"               # ignore this 
PEERROUTES="yes"            # ignore this 
IPV4_FAILURE_FATAL="no"     # ignore this 
IPV6INIT="yes"              # ignore this 
IPV6_AUTOCONF="yes"         # ignore this 
IPV6_DEFROUTE="yes"         # ignore this 
IPV6_PEERDNS="yes"          # ignore this 
IPV6_PEERROUTES="yes"       # ignore this 
IPV6_FAILURE_FATAL="no"     # ignore this 
NAME="enp0s3"
UUID="c107488a-e080-4f46-a6a6-241d2e5e8846"
ONBOOT="yes"                      # this means that the network must be up at bootup. 
</pre>

As you can see that this file does not give any of the following information:




<ul>
	<li>IPADDR0 - You can also have IPADDR1, IPADDR2,…etc</li>
	<li>GATEWAY0 - You can also have GATEWAY1, GATEWAY2,…etc</li>
	<li>PREFIX0 - This is the subnet mask, aka netmask, e.g. can have value 24. You can also have PREFIX1, PREFIX2,…etc</li>
</ul>

That is because BOOTPROTO setting is set to ”dhcp” rather than “none”, which means that the dhcp handles all this info. 


You can edit this file directly, (if so then you will need to restart the interface using the ifdown and ifup command, which is covered further down), or you can use the gnome gui interface:

<a href="http://codingbee.net/wp-content/uploads/2015/04/olDLBse.png"><img src="http://codingbee.net/wp-content/uploads/2015/04/olDLBse.png" alt="" width="364" height="281" class="alignnone size-full wp-image-4075" /></a>


<a href="http://codingbee.net/wp-content/uploads/2015/04/NkgeSQ0.png"><img src="http://codingbee.net/wp-content/uploads/2015/04/NkgeSQ0.png" alt="" width="764" height="401" class="alignnone size-full wp-image-4076" /></a>

<a href="http://codingbee.net/wp-content/uploads/2015/04/sRNvIoF.png"><img src="http://codingbee.net/wp-content/uploads/2015/04/sRNvIoF.png" alt="" width="743" height="550" class="alignnone size-full wp-image-4078" /></a>

<a href="http://codingbee.net/wp-content/uploads/2015/04/JeQZ16v.png"><img src="http://codingbee.net/wp-content/uploads/2015/04/JeQZ16v.png" alt="" width="737" height="814" class="alignnone size-full wp-image-4079" /></a>








For example, while dhcp is enabled we have:


<pre>
$ cat ifcfg-enp0s3
HWADDR="08:00:27:82:83:FC"
TYPE="Ethernet"
BOOTPROTO="dhcp"
DEFROUTE="yes"
IPV4_FAILURE_FATAL="no"
IPV6INIT="yes"
IPV6_AUTOCONF="yes"
IPV6_DEFROUTE="yes"
IPV6_FAILURE_FATAL="no"
NAME="enp0s3"
UUID="c107488a-e080-4f46-a6a6-241d2e5e8846"
ONBOOT="yes"
PEERDNS=yes
PEERROUTES=yes
IPV6_PEERDNS=yes
IPV6_PEERROUTES=yes
</pre>

Now if we use the gnome gui to disable dhcp and apply the following settings:

<a href="http://codingbee.net/wp-content/uploads/2015/04/DZGqUDA.png"><img src="http://codingbee.net/wp-content/uploads/2015/04/DZGqUDA.png" alt="" width="740" height="918" class="alignnone size-full wp-image-4080" /></a>


Then the config file changes to:


<pre>
$ cat ifcfg-enp0s3
HWADDR="08:00:27:82:83:FC"
TYPE="Ethernet"
BOOTPROTO=none              # DHCP is disabled   
DEFROUTE="yes"
IPV4_FAILURE_FATAL="no"
IPV6INIT="yes"
IPV6_AUTOCONF="yes"
IPV6_DEFROUTE="yes"
IPV6_FAILURE_FATAL="no"
NAME="enp0s3"
UUID="c107488a-e080-4f46-a6a6-241d2e5e8846"
ONBOOT="yes"
IPADDR=192.168.1.10                # ip address is now static
PREFIX=28                          # prefix is now static
GATEWAY=192.168.1.35               # gateway ip is now now static                        
DNS1=8.8.8.8                       # dns is now static. 
IPV6_PEERDNS=yes
IPV6_PEERROUTES=yes

</pre>



<h2>The resolv config file</h2>

This file is automatically generated (and used internally) by network manager. It is derived from the content in the network config file, e.g. ifcfg-enp0s3.

<pre>
$ cat /etc/resolv.conf
# Generated by NetworkManager
search codingbee.dyndns.org
nameserver 192.168.1.1
</pre>

Never make any changes to the resolv.config file.


<pre>
$ less /usr/share/doc/initscripts-*/sysconfig.txt             
</pre>

This is a help file that describes the role of various networking script files.


<h2>Stopping and starting an interface</h2>

If you make any changes to a network file, then remember that you need to restart the network interface card for the changes to take effect. this is done using the ifdown and ifup commands. Here it is in action:

<pre>
$ nmcli con show
NAME           UUID                                  TYPE            DEVICE
System enp0s3  ad2de184-098f-4eb8-92af-3e6c086f1186  802-3-ethernet  enp0s3
$ ifdown enp0s3
Device 'enp0s3' successfully disconnected.
$ nmcli con show
NAME           UUID                                  TYPE            DEVICE
System enp0s3  ad2de184-098f-4eb8-92af-3e6c086f1186  802-3-ethernet  --
$ ifup enp0s3
Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/4)
$ nmcli con show
NAME           UUID                                  TYPE            DEVICE
System enp0s3  ad2de184-098f-4eb8-92af-3e6c086f1186  802-3-ethernet  enp0s3
</pre>

Starting/stopping the interface will be reflected a tiny cross appearing on the network icon on the top right of the gnome interface. Also this will disconnect any ssh/putty sessions. 

Here we used nmcli to view the status. we could have also used nmcli instead of ifup/ifdown commands:


<pre>

$ nmcli con show
NAME           UUID                                  TYPE            DEVICE
System enp0s3  ad2de184-098f-4eb8-92af-3e6c086f1186  802-3-ethernet  enp0s3
$ nmcli con down "System enp0s3"
Connection 'System enp0s3' successfully deactivated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/5)
$ nmcli con up "System enp0s3"
Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/6)
$ nmcli con show
NAME           UUID                                  TYPE            DEVICE
System enp0s3  ad2de184-098f-4eb8-92af-3e6c086f1186  802-3-ethernet  enp0s3
</pre>


Note, starting/stopping a network device using nmcli or ifup/ifdown won't survive a reboot.  In order to survive a reboot you need to edit the device's configuration settings file, which is located in <code>/etc/sysconfig/network-scripts/</code>. 

Alternatively you can make this persistant by using the nmcli command like this:


<pre>
$ nmcli connection modify System\ enp0s3 connection.autoconnect no
$ cat ifcfg-enp0s3 | grep ONBOOT                            ONBOOT=no
$ nmcli connection modify System\ enp0s3 connection.autoconnect yes
$ cat ifcfg-enp0s3 | grep ONBOOT                            ONBOOT=yes
</pre> 

the nmcli command is long wielded, but luckily you can use the tab+tab autocomplete to make things easier.