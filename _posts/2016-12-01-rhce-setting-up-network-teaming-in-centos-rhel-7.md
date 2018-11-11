---
ID: 593
post_title: 'RHCE &#8211; Setting up Network Teaming in CentOS/RHEL 7'
author: sher
post_excerpt: "In CentOS it's possible to configure two separate network interfaces (e.g. enp0s3 and enp0s8) to work together. This is useful for 3 main reasons improved performance in terms of increased bandwidth, load balancing, and fault tolerance.  "
layout: post
permalink: >
  https://codingbee.net/tutorials/rhce/rhce-setting-up-network-teaming-in-centos-rhel-7
published: true
post_date: 2016-12-01 00:00:00
---
Network interfaces, e.g. eth0 and eth1, traditionally represents actual hardware inside your machine. These hardware components are commonly referred to as network adapters and they come in various shapes and sizes. Some network adapters have dual sockets.

CentOS assigns a user friendly name to these sockets (aka interfaces) called, eth0, eth1,....etc. You can view a full list of all the interfaces on you machine by running:

<pre>
[root@localhost ~]# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 08:00:27:d0:c3:19 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic enp0s3
       valid_lft 86376sec preferred_lft 86376sec
    inet6 fe80::ec1:d80f:6948:87b6/64 scope link
       valid_lft forever preferred_lft forever
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 08:00:27:ab:eb:2e brd ff:ff:ff:ff:ff:ff
    inet 172.28.128.3/24 brd 172.28.128.255 scope global dynamic enp0s8
       valid_lft 1176sec preferred_lft 1176sec
    inet6 fe80::a00:27ff:feab:eb2e/64 scope link
       valid_lft forever preferred_lft forever
4: enp0s9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 08:00:27:75:e3:89 brd ff:ff:ff:ff:ff:ff
    inet 172.28.128.4/24 brd 172.28.128.255 scope global dynamic enp0s9
       valid_lft 1176sec preferred_lft 1176sec
    inet6 fe80::a00:27ff:fe75:e389/64 scope link
       valid_lft forever preferred_lft forever
5: virbr0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN qlen 1000
    link/ether 52:54:00:9c:e9:92 brd ff:ff:ff:ff:ff:ff
    inet 192.168.122.1/24 brd 192.168.122.255 scope global virbr0
       valid_lft forever preferred_lft forever
6: virbr0-nic: <BROADCAST,MULTICAST> mtu 1500 qdisc pfifo_fast master virbr0 state DOWN qlen 1000
    link/ether 52:54:00:9c:e9:92 brd ff:ff:ff:ff:ff:ff
</pre>



In CentOS it's possible to configure two separate network interfaces (e.g. enp0s3 and enp0s8) to work together. This is handy in a number of ways:

<ul>
 	<li><strong>combined bandwidth</strong> - This means improved performance</li>
 	<li><strong>load balancing</strong> - So that one interface doesn't do all the work while the other is idle</li>
 	<li><strong>fault tolerance</strong> - if one interface suffers a hardware failure then the other interface can take over</li>
</ul>

You can follow along this article by using our <a href="https://github.com/Sher-Chowdhury/cento7-network-teaming-test-vm">Network Teaming Vagrant project on Github</a>.

Combining two network interfaces like this is referred to as 'Link Aggregation'.

There are 2 methods for setting up link aggregation, they are called:
<ul>
 	<li>network bonding - this will eventually get replaced by network teaming</li>
 	<li>network teaming</li>
</ul>


The 'teaming' method  is new in RHEL 7 and has more features compared to the bonding method. Therefore in this guide we'll focus on network teaming. First off, you need to install the teamd rpm:

<pre>
$ yum install teamd
</pre>

A team based link aggregation has 5 different operating modes:
<ul>
 	<li>broadcast</li>
 	<li>roundrobin</li>
 	<li>activebackup</li>
 	<li>loadbalance</li>
 	<li>lacp</li>
</ul>

You can find more info about these modes in the teamd man pages:

<pre>
$ man 5 teamd.conf
</pre>


You can setup Link aggregation using NetworkManager utilities. In our case we'll use nmcli.

<pre>
$ nmcli connection add type team con-name CodingBeeTeam0 ifname CodingBeeTeam0 config '{"runner": {"name":"activebackup"}}'
Connection 'CodingBeeTeam0' (7a12239f-ca6f-4347-b9db-86122bb22078) successfully added.
</pre>

This is quite a complex looking command, but you can see examples of this command in:

<pre>
$ man nmcli-examples
$ man teamd.conf   # gives json examples. 
</pre>




Here we have created a new connection called 'CodingBeeTeam0' as well as an 'emulated' interface also called 'CodingBeeTeam0'. To view the connection you can do:

<pre>
$ nmcli connection show CodingBeeTeam0
</pre>

The corresponding file that's been created is:


<pre>
$ cat /etc/sysconfig/network-scripts/ifcfg-CodingBeeTeam0
DEVICE=CodingBeeTeam0
TEAM_CONFIG="{\"runner\": {\"name\":\"activebackup\"}}"
DEVICETYPE=Team
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
NAME=CodingBeeTeam0
UUID=7a12239f-ca6f-4347-b9db-86122bb22078
ONBOOT=yes
</pre>

As for the 'emulated' interface, you can view this using the ip command:

<pre>
$ ip addr show CodingBeeTeam0
8: CodingBeeTeam0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN qlen 1000
    link/ether 52:1e:05:19:50:0a brd ff:ff:ff:ff:ff:ff
</pre>


This is more of a simulated simulated interface because it's not linked to any actual hardware. Instead the CodingBeeTeam0 connection will manage traffic from other actual interfaces, such as eth0, enp0s3, wlan0, etc. Hence CodingBeeTeam0 can be thought of a 'virtual interface' that carries data traffic to/from other real interfaces. This interface is often referred to as the <strong>team-interface</strong>   

This virtual interfaces uses something called 'runners' which you can imagine as an army of ants that carrys data traffic between the team-interface and the other real interfaces. You can control the logic that these runners uses when deciding which interface it should send/retrieve traffic, by specifying the operation mode. In our case we have chosen activebackup. Note <code>'{"runner": {"name":"activebackup"}}'</code> is actually written in json syntax. 


In order for our new team connection, CodingBeeTeam0, to be able to start receiving traffic, we need to (manually) assign it with an ip address, which we can do like this:

<pre>
$ nmcli connection modify CodingBeeTeam0 ipv4.addresses '192.168.2.50/24'
</pre>

It doesn't matter what ip address you assign to it, as long as it falls inside any of the <a href="https://en.wikipedia.org/wiki/Private_network">private network</a> ranges. 

This ends up adding the following line to the the file:

<pre>
$  cat /etc/sysconfig/network-scripts/ifcfg-CodingBeeTeam0
DEVICE=CodingBeeTeam0
TEAM_CONFIG="{\"runner\": {\"name\":\"activebackup\"}}"
DEVICETYPE=Team
BOOTPROTO=dhcp
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
NAME=CodingBeeTeam0
UUID=7a12239f-ca6f-4347-b9db-86122bb22078
ONBOOT=yes
<strong>IPADDR=192.168.2.50
PREFIX=24</strong>
PEERDNS=yes
PEERROUTES=yes
IPV6_PEERDNS=yes
IPV6_PEERROUTES=yes
</pre>


Since we have manually assigned an IP address (rather than getting dhcp to automatically assign it with an IP address), it means that before using this connection we first need to disable dhcp for this connection:

<pre>
$ nmcli connection modify CodingBeeTeam0 ipv4.method manual
</pre> 

This ends up changing the <strong>following</strong> line:

<pre>
$ cat /etc/sysconfig/network-scripts/ifcfg-CodingBeeTeam0
DEVICE=CodingBeeTeam0
TEAM_CONFIG="{\"runner\": {\"name\":\"activebackup\"}}"
DEVICETYPE=Team
<strong>BOOTPROTO=none</strong>
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
NAME=CodingBeeTeam0
UUID=7a12239f-ca6f-4347-b9db-86122bb22078
ONBOOT=yes
IPADDR=192.168.2.50
PREFIX=24
IPV6_PEERDNS=yes
IPV6_PEERROUTES=yes

</pre>


We can now take a more detailed look of our team connection, CodingBeeTeam0, using the teamdctl command:

<pre>
[root@localhost ~]# teamdctl CodingBeeTeam0 state
setup:
  runner: activebackup
runner:
  active port:
</pre>

However we still haven't specified which actual interfaces (e.g. enp0s8 and enp0s9) the team connection (CodingBeeTeam0) should be routing traffic to. We can set this using the nmcli command, for example here's the command to attach enp0s8 to CodingBeeTeam0:


<pre>
$ nmcli connection add type team-slave con-name CodingBeeTeam0-port1 ifname enp0s8 master CodingBeeTeam0
Connection 'CodingBeeTeam0-port1' (ffefdb3c-d61b-4800-9495-de9af09e62e7) successfully added.
</pre> 

Here we are telling nmcli to "create a new team-slave connection using the existing interface, enp0s8, and give this team-slave the name 'CodingBeeTeam0-port1', then attach that team-slave connection to it's master team-connection, called 'CodingBeeTeam0'"   

We need to attach at least 2 interfaces to our team connection, so let's do the same thing with enp0s9:

<pre>
$ nmcli connection add type team-slave con-name CodingBeeTeam0-port2 ifname enp0s9 master CodingBeeTeam0
Connection 'CodingBeeTeam0-port2' (b2f6d366-6ddf-471f-bc86-aee143b29b1b) successfully added.
</pre> 

Now if we check our team-connection's state again, we see:

<pre>
[root@localhost ~]# teamdctl CodingBeeTeam0 state
setup:
  runner: activebackup
runner:
  active port:
</pre>

Nothing has changed, that's because we need to activate both the team-slave connections, and the team connection itself. To activate everything we do:

<pre>
$ nmcli connection up
$ nmcli connection up CodingBeeTeam0-port1
Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/7)
$ nmcli connection up CodingBeeTeam0-port2
Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/8)
</pre>

Now if we check the states again, we see:

<pre>
$ teamdctl CodingBeeTeam0 state
setup:
  runner: activebackup
ports:
  enp0s8
    link watches:
      link summary: up
      instance[link_watch_0]:
        name: ethtool
        link: up
        down count: 0
  enp0s9
    link watches:
      link summary: up
      instance[link_watch_0]:
        name: ethtool
        link: up
        down count: 0
runner:
  active port: enp0s8
</pre>

This is also indicated when we run:


<pre>
$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 08:00:27:d0:c3:19 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic enp0s3
       valid_lft 79552sec preferred_lft 79552sec
    inet6 fe80::ec1:d80f:6948:87b6/64 scope link
       valid_lft forever preferred_lft forever
<strong>3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast master CodingBeeTeam0 state UP qlen 1000
    link/ether 08:00:27:ab:eb:2e brd ff:ff:ff:ff:ff:ff
4: enp0s9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast master CodingBeeTeam0 state UP qlen 1000
    link/ether 08:00:27:ab:eb:2e brd ff:ff:ff:ff:ff:ff</strong>
5: virbr0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN qlen 1000
    link/ether 52:54:00:9c:e9:92 brd ff:ff:ff:ff:ff:ff
    inet 192.168.122.1/24 brd 192.168.122.255 scope global virbr0
       valid_lft forever preferred_lft forever
6: virbr0-nic: <BROADCAST,MULTICAST> mtu 1500 qdisc pfifo_fast master virbr0 state DOWN qlen 1000
    link/ether 52:54:00:9c:e9:92 brd ff:ff:ff:ff:ff:ff
<strong>7: CodingBeeTeam0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP qlen 1000
    link/ether 08:00:27:ab:eb:2e brd ff:ff:ff:ff:ff:ff
    inet 192.168.2.50/24 brd 192.168.2.255 scope global CodingBeeTeam0
       valid_lft forever preferred_lft forever
    inet6 fe80::7b52:6256:17cd:a47d/64 scope link
       valid_lft forever preferred_lft forever</strong>
</pre>



Now we can take down a interface and see the failover in action:


<pre>
$ nmcli connection down CodingBeeTeam0-port1
Connection 'CodingBeeTeam0-port1' successfully deactivated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/7)
$ teamdctl CodingBeeTeam0 state
setup:
  runner: activebackup
ports:
  enp0s9
    link watches:
      link summary: up
      instance[link_watch_0]:
        name: ethtool
        link: up
        down count: 0
runner:
  active port: enp0s9
</pre>


As an alternative to using nmcli, you can also do all this via the gui. To start it up, make sure you are in the gnome desktop, then start up the gui by running:

<pre>
$ nm-connection-editor
</pre>


&nbsp;

&nbsp;
<h2>Useful resources</h2>
https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Networking_Guide/ch-Configure_Network_Teaming.html




[post-content post_name=rhsca-quiz] 

[accordion]
[toggle title="What is Link Aggregation?"]
Combining two network interfaces to act like one.
[/toggle]
[toggle title="What are the advantages of Link Aggregation?"]
- double bandwidth
- load balancing
- redundancies
[/toggle]
[toggle title="What are the main methods available for setting up Link Aggregation?"]
- network bonding
- network teaming
[/toggle]
[toggle title="Out of those methods, which is prefered and why?"]
network teaming - it's newer and has more features
[/toggle]
[toggle title="What are the operating modes available for team bonding?"]
- broadcast
- roundrobin
- activebackup
- loadbalance
[/toggle]
[toggle title="Where can you find more info about these operating modes?"]
$ man 5 teamd.conf
[/toggle]
[toggle title="What are the steps involved in setting up a network team?"]

1. Create new connection of the type 'team'. 
2. Manually assign an ip address (that falls in one of the private ranges) to our new team connection
3. Completely disable dhcp for our new team connection. That's to avoid dhcp overriding our manual ip4 address. 
4. Create new connection of type team-slave for both enp0s8 and enp0s9 
5. Activate both team-slave connections and master connection
6. Test everything is working. 

[/toggle]
[toggle title="What is a command to create a new activebackup based connection called cbteam?"]
$ nmcli connection add type team con-name cbteam ifname cbteam config '{"runner": {"name":"activebackup"}}'

4 arguments:
type: team
con-name: 
if-name:
config:
[/toggle]
[toggle title="What is the end result of running the above command?"]
- new 'virtual' interface is created. It appears in 'ip addr show'
- new connection file created
[/toggle]
[toggle title="What is 2 commands can give more info about this new team-interface?"]
$ teamdctl cbteam state
# and 
$ ip address show cbteam
[/toggle]
[toggle title="What is the command to manually assign an ip address of 192.168.2.50/24 to our new connection?"]
$ nmcli connection modify cbteam ipv4.addresses '192.168.2.50/24' 
[/toggle]
[toggle title="What's the command to disable the dhcp from overriding our manually set ip address?"]
$ nmcli connection modify cbteam ipv4.method manual
[/toggle]
[toggle title="What is the command to create new team-slave connection enp0s8 and enp0s9?"]
$ nmcli connection add type team-slave con-name cbteam_enp0s8 ifname enp0s8 master cbteam
$ nmcli connection add type team-slave con-name cbteam_enp0s9 ifname enp0s9 master cbteam

4 arguements:
1. connection type (team-slave)
2. connection name (e.g. cbteam_enp0s8)
3. interface name (enp0s8)
4. what is the master's name (cbteam)
[/toggle]
[toggle title="what's the to activate our new team connection?"]
$ nmcli connection up cbteam
$ nmcli connection up cbteam_enp0s8
$ nmcli connection up cbteam_enp0s9
[/toggle]
[toggle title="What commands can run to check that the new team is working?"]
$ teamdctl cbteam state
[/toggle]
[toggle title="what command do you need to run to setup network teaming via the Gnome gui?"]
$ nm-connection-editor
[/toggle]
[/accordion]