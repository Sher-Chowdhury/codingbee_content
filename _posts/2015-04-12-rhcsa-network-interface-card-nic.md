---
ID: 358
post_title: 'RHCSA &#8211; Network Interface Card (NIC)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-network-interface-card-nic
published: true
post_date: 2015-04-12 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="Which directory contains all the network connections config files?"]
/etc/sysconfig/network-scripts/
[/toggle]
[toggle title="What do you need to do to ensure that the interface, enp0s3 starts up during boot time?"]
# Set the entry:
ONBOOT=yes
# in the file:
/etc/sysconfig/network-scripts/ifcfg-enp0s3
[/toggle]
[toggle title="What is the nmcli command to apply the above setting?"]
# First locate the connection:
$ nmcli connection show
# Then apply the setting:
$ nmcli connection modify {connection-name} connection.autoconnect yes
[/toggle]
[/accordion]

<hr/>


In rhel, there are 3 different naming conventions available for labelling your machine NIC:

<ul>
	<li><strong>BIOS naming</strong> - names are based on hardware property of a given NIC. For example:
<ul>
	<li>em1, em2, ...etc - names for embedded NICs, i.e NICs that are integrated into the motherboard.</li>
	<li>p<slot-number>p<port-number> - e.g. p4p2, this mean's the fourth pci slot on the motherboard, and the second ethernet port on the NIC, since you can buy <a href="http://www.amazon.co.uk/s/ref=nb_sb_noss_2?url=node%3D430513031&field-keywords=4+port&rh=n%3A340831031%2Cn%3A428655031%2Cn%3A430513031%2Ck%3A4+port">NICs that contains upto 4 ports</a>, hence let's you plug in up 4 ethernet cables.</li>
</ul></li>
	<li><strong>udev naming</strong> - e.g. eth0, eth1. This naming convention is not that informative. </li>
	<li><strong>Physical naming</strong> - same as bios naming. </li>
	<li><strong>logical naming</strong> - vlan and alias configurations. Not important for RHCSA course</li>
</ul>



<h2>Autoconnect Interfaces at boot time</h2>

Your network interfaces can be configured to automatically start them up at boot time. 

In order to autostart an interface at boot time, you need to tell Network Manager which interfaces should be started up at boot time. This in turn is dictated by which connections (config files) are going to be activated at boot time. This is controlled by each connection's "ONBOOT" setting. 


So an easy way to identify which interfaces are going to be activated at boot time is by viewing the connection files, like this:


<pre>
$ grep -R "ONBOOT"  /etc/sysconfig/network-scripts/ifcfg-*
/etc/sysconfig/network-scripts/ifcfg-CustCon:ONBOOT=no
/etc/sysconfig/network-scripts/ifcfg-enp0s3:ONBOOT=yes
/etc/sysconfig/network-scripts/ifcfg-enp0s3-static:ONBOOT=no
/etc/sysconfig/network-scripts/ifcfg-enp0s8:ONBOOT=yes
/etc/sysconfig/network-scripts/ifcfg-enp0s8-1:ONBOOT=yes
/etc/sysconfig/network-scripts/ifcfg-lo:ONBOOT=yes
</pre>
 
Therefore for a given interface, if there is connection associated to it which contains the setting "ONBOOT=yes", then than interface will start up. But just to be sure, you also need to ensure that the connection you are interested in is also managed by NetworkManager:


<pre>
 nmcli -p con show
=============================================================================
                     NetworkManager connection profiles
=============================================================================
NAME           UUID                                  TYPE            DEVICE
-----------------------------------------------------------------------------
System enp0s3  ad2de184-098f-4eb8-92af-3e6c086f1186  802-3-ethernet  enp0s3
enp0s3-static  0d936343-5c8e-4545-9174-f295b6c9ae86  802-3-ethernet  --
enp0s8         7607287c-31fd-4cb3-b663-a4e4120ef265  802-3-ethernet  --
CustCon        991d8f70-7f92-481e-9202-c7afe5a434c0  802-3-ethernet  --
</pre>

And also that the Network Manager service is running and enabled:


<pre>
$ systemctl status NetworkManager
NetworkManager.service - Network Manager
   Loaded: loaded (/usr/lib/systemd/system/NetworkManager.service; enabled)
   Active: active (running) since Sun 2015-10-04 15:06:32 BST; 20min ago
 Main PID: 5496 (NetworkManager)
   CGroup: /system.slice/NetworkManager.service
           ├─5496 /usr/sbin/NetworkManager --no-daemon
           └─5506 /sbin/dhclient -d -q -sf /usr/libexec/nm-dhcp-helper -pf /var/run/dhclient-enp0s3....

Oct 04 15:06:32 puppetmaster.local dhclient[5506]: bound to 10.0.2.15 -- renewal in 32475 seconds.
Oct 04 15:06:32 puppetmaster.local NetworkManager[5496]: <info>  (enp0s3): device state change: ip...0]
Oct 04 15:06:32 puppetmaster.local NetworkManager[5496]: <info>  (enp0s3): device state change: se...0]
Oct 04 15:06:32 puppetmaster.local NetworkManager[5496]: <info>  NetworkManager state is now CONNE...AL
Oct 04 15:06:32 puppetmaster.local NetworkManager[5496]: <info>  NetworkManager state is now CONNE...TE
Oct 04 15:06:32 puppetmaster.local NetworkManager[5496]: <info>  NetworkManager state is now CONNE...AL
Oct 04 15:06:32 puppetmaster.local NetworkManager[5496]: <info>  (enp0s3): Activation: successful,...d.
Oct 04 15:07:03 puppetmaster.local NetworkManager[5496]: <info>  (enp0s3): Activation: Stage 4 of .....
Oct 04 15:07:03 puppetmaster.local NetworkManager[5496]: <info>  (enp0s3): Activation: Stage 4 of .....
Oct 04 15:07:03 puppetmaster.local NetworkManager[5496]: <info>  (enp0s3): Activation: Stage 4 of ...e.
Hint: Some lines were ellipsized, use -l to show in full.


</pre>


Now if there is a connection that has the ONBOOT=NO setting.

Let's say we have:

<pre>
$ cat /etc/sysconfig/network-scripts/ifcfg-enp0s3-static
TYPE=Ethernet
BOOTPROTO=dhcp
DEFROUTE=yes
PEERDNS=yes
PEERROUTES=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_PEERDNS=yes
IPV6_PEERROUTES=yes
IPV6_FAILURE_FATAL=no
NAME=enp0s3-static
UUID=0d936343-5c8e-4545-9174-f295b6c9ae86
DEVICE=enp0s3
<strong>ONBOOT=no</strong>
</pre>


Let's say we want to enable this connection so that the interface autostarts at the next reboot with this connection, then you run the following command:


<pre>
$ nmcli connection modify enp0s3-static connection.autoconnect yes
</pre>

To confirm this command has worked we do:

<pre>
$ cat /etc/sysconfig/network-scripts/ifcfg-enp0s3-static | grep ONBOOT
ONBOOT=yes
</pre>

Another thing you need to is see if there are any other connections for this interface, if so then run the nmcli command to turn off onboot for them, so that you only have a singe connection for the given device, which has the setting "ONBOOT=yes".