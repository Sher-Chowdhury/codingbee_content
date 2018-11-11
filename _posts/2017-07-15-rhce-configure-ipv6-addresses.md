---
ID: 2053
post_title: 'RHCE &#8211; Configure IPv6 addresses'
author: sher
post_excerpt: >
  An interface device usually has IPv4
  address assigned to it. However you can
  assign an IPv6 address to it too.
layout: post
permalink: >
  https://codingbee.net/tutorials/rhce/rhce-configure-ipv6-addresses
published: true
post_date: 2017-07-15 19:15:44
---
An interface device usually has IPv4 address assigned to it. However you can assign an IPv6 address to it too. Here's a step-by-step approach to do this. First you need to have a ip6 enabled ifcfg-* file (aka connection). Let's first see what connections currently exist:

<pre>
$ ls -l /etc/sysconfig/network-scripts/ifcfg-*
-rw-r--r--. 1 root    root    312 Jul 15 12:12 /etc/sysconfig/network-scripts/ifcfg-enp0s3
-rw-------. 1 vagrant vagrant 162 Jul 15 12:12 /etc/sysconfig/network-scripts/ifcfg-enp0s8
-rw-------. 1 vagrant vagrant 162 Jul 15 12:12 /etc/sysconfig/network-scripts/ifcfg-enp0s9
-rw-r--r--. 1 root    root    254 Sep 12  2016 /etc/sysconfig/network-scripts/ifcfg-lo
</pre>

Another way to get this info is:

<pre>
$ nmcli connection show
NAME           UUID                                  TYPE            DEVICE
System enp0s8  00cb8299-feb9-55b6-a378-3fdc720e0bc6  802-3-ethernet  enp0s8
System enp0s9  93d13955-e9e2-a6bd-df73-12e3c747f122  802-3-ethernet  enp0s9
enp0s3         5c9ead4e-5e5c-4f96-8057-a13953c2c2f3  802-3-ethernet  enp0s3
virbr0         bfb2895c-46a8-4dc5-bf72-e714a358acdc  bridge          virbr0
</pre>

This not only lists all the connections, but shows which connections are currently attached to a device. In our case all our connections are in use. 

You can edit an existing connection (i.e. edit existing ifcfg-* file) or create a new connection. In our case we'll play it safe, and create a new connection:


<pre>
$ nmcli connection add con-name codingbee-enp0s8 type ethernet ifname enp0s8
</pre>

This command ends up creating the following new connection:

<pre>
[root@localhost network-scripts]# cat /etc/sysconfig/network-scripts/ifcfg-codingbee-enp0s8
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
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=codingbee-enp0s8
UUID=7bc1c537-7546-4976-b81a-60912f8416aa
DEVICE=enp0s8
ONBOOT=yes
</pre>

Now if we list our connections again, we'll see:


<pre>
$ [root@localhost network-scripts]# nmcli connection show
NAME              UUID                                  TYPE            DEVICE
System enp0s8     00cb8299-feb9-55b6-a378-3fdc720e0bc6  802-3-ethernet  enp0s8
System enp0s9     93d13955-e9e2-a6bd-df73-12e3c747f122  802-3-ethernet  enp0s9
enp0s3            5c9ead4e-5e5c-4f96-8057-a13953c2c2f3  802-3-ethernet  enp0s3
virbr0            bfb2895c-46a8-4dc5-bf72-e714a358acdc  bridge          virbr0
<strong>codingbee-enp0s8  7bc1c537-7546-4976-b81a-60912f8416aa  802-3-ethernet  --</strong>
</pre>

As you can see, the connection is available, but it isn't currently being used by any interfaces. 

Before we can start using it, we first need to assign it with ipv4 ip address. I'm going to choose the ip '192.168.122.10/24':

<pre>
$ nmcli connection modify codingbee-enp0s8 ipv4.addresses 192.168.122.10/24
</pre>


This ends up adding the following lines:

<pre>
[root@localhost network-scripts]# pwd
/etc/sysconfig/network-scripts
[root@localhost network-scripts]# cat ifcfg-codingbee-enp0s8
TYPE=Ethernet
BOOTPROTO=dhcp
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=codingbee-enp0s8
UUID=7bc1c537-7546-4976-b81a-60912f8416aa
DEVICE=enp0s8
ONBOOT=yes
<strong>IPADDR=192.168.122.10</strong>
<strong>PREFIX=24</strong>
PEERDNS=yes
PEERROUTES=yes
IPV6_PEERDNS=yes
IPV6_PEERROUTES=yes
<strong>RES_OPTIONS=single-request-reopen</strong>
</pre>

Next we disable dhcp:

<pre>
$ nmcli connection modify codingbee-enp0s8 ipv4.method manual
</pre>

This ends up making the following changes:


<pre>
[root@localhost network-scripts]# pwd
/etc/sysconfig/network-scripts
[root@localhost network-scripts]# cat ifcfg-codingbee-enp0s8
TYPE=Ethernet
<strong>BOOTPROTO=none</strong>
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=codingbee-enp0s8
UUID=7bc1c537-7546-4976-b81a-60912f8416aa
DEVICE=enp0s8
ONBOOT=yes
RES_OPTIONS=single-request-reopen
IPADDR=192.168.122.10
PREFIX=24
IPV6_PEERDNS=yes
IPV6_PEERROUTES=yes
<del>PEERDNS=yes
PEERROUTES=yes</del>
</pre>

Next we set the ipv6 address manually:

<pre>
$ nmcli connection modify codingbee-enp0s8 ipv6.addresses 2001:db8:85a3:0:0:8a2e:370:7334/64
</pre>

This ends up making the following change:


<pre>
cat /etc/sysconfig/network-scripts/ifcfg-codingbee-enp0s8
TYPE=Ethernet
BOOTPROTO=none
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=codingbee-enp0s8
UUID=7bc1c537-7546-4976-b81a-60912f8416aa
DEVICE=enp0s8
ONBOOT=yes
RES_OPTIONS=single-request-reopen
<strong>IPV6ADDR=2001:db8:85a3::8a2e:370:7334/64</strong>
IPADDR=192.168.122.10
PREFIX=24
IPV6_PEERDNS=yes
IPV6_PEERROUTES=yes
</pre>


Then we specify that the ipv6 address being done manually:

<pre>
$ nmcli connection modify codingbee-enp0s8 ipv6.method manual
</pre>

This ends up changing the following:

<pre>
$ cat /etc/sysconfig/network-scripts/ifcfg-codingbee-enp0s8
TYPE=Ethernet
BOOTPROTO=none
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=no
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=codingbee-enp0s8
UUID=7bc1c537-7546-4976-b81a-60912f8416aa
DEVICE=enp0s8
ONBOOT=yes
RES_OPTIONS=single-request-reopen
<strong>IPV6ADDR=2001:db8:85a3::8a2e:370:7334/64</strong>
IPADDR=192.168.122.10
PREFIX=24
<del>IPV6_PEERDNS=yes
IPV6_PEERROUTES=yes</del>
</pre>

This connection is now ready to be used. We can now reload the enp0s8 with the new connection setting:


<pre>
$ nmcli connection up codingbee-enp0s8
Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/6)
</pre>


Now we can confirm that this has worked like this:



<pre>
$ nmcli connection show
NAME              UUID                                  TYPE            DEVICE
System enp0s9     93d13955-e9e2-a6bd-df73-12e3c747f122  802-3-ethernet  enp0s9
codingbee-enp0s8  7bc1c537-7546-4976-b81a-60912f8416aa  802-3-ethernet  enp0s8
enp0s3            5c9ead4e-5e5c-4f96-8057-a13953c2c2f3  802-3-ethernet  enp0s3
virbr0            9d1bce28-bb88-4113-8922-6dbeee1f301f  bridge          virbr0
System enp0s8     00cb8299-feb9-55b6-a378-3fdc720e0bc6  802-3-ethernet  --


$ ip address show enp0s8
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 08:00:27:45:e9:74 brd ff:ff:ff:ff:ff:ff
    inet 192.168.122.10/24 brd 192.168.122.255 scope global enp0s8
       valid_lft forever preferred_lft forever
    inet6 2001:db8:85a3::8a2e:370:7334/64 scope global
       valid_lft forever preferred_lft forever
    inet6 fe80::1c25:c04d:f3fc:1529/64 scope link
       valid_lft forever preferred_lft forever
</pre>


<h2>Testing network interfaces with ping and ping6</h2>

Let's say we have the following network interface:



<pre>
$ ip addr show enp0s8
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 08:00:27:16:bc:41 brd ff:ff:ff:ff:ff:ff
    inet 192.168.56.103/24 brd 192.168.56.255 scope global dynamic enp0s8
       valid_lft 1103sec preferred_lft 1103sec
    inet6 fe80::a00:27ff:fe16:bc41/64 scope link
       valid_lft forever preferred_lft forever
</pre>

Based on the above, this interface is currently up and is accessible via an ipv4 and ipv6 ip addresses. There are 2 sets of configuration settings applied to this, one set for ipv4 and the other for ipv6. To check if this interface is working properly, we need to test both sets of configurations. First to test the ipv4 settings we use the ping command as follows:

<pre>
$ ping -c 3 192.168.56.103
PING 192.168.56.103 (192.168.56.103) 56(84) bytes of data.
64 bytes from 192.168.56.103: icmp_seq=1 ttl=64 time=0.260 ms
64 bytes from 192.168.56.103: icmp_seq=2 ttl=64 time=0.436 ms
64 bytes from 192.168.56.103: icmp_seq=3 ttl=64 time=0.068 ms

--- 192.168.56.103 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2001ms
rtt min/avg/max/mdev = 0.068/0.254/0.436/0.151 ms
</pre>


Next to test the ipv6 related settings, we use ping6:


<pre>
$ ping6 -c 3 -I enp0s8 fe80::a00:27ff:fe16:bc41
PING fe80::a00:27ff:fe16:bc41(fe80::a00:27ff:fe16:bc41) from fe80::a00:27ff:fe16:bc41 enp0s8: 56 data bytes
64 bytes from fe80::a00:27ff:fe16:bc41: icmp_seq=1 ttl=64 time=0.390 ms
64 bytes from fe80::a00:27ff:fe16:bc41: icmp_seq=2 ttl=64 time=0.491 ms
64 bytes from fe80::a00:27ff:fe16:bc41: icmp_seq=3 ttl=64 time=0.091 ms
</pre>

Note: To use ping6, It is mandatory to explicitly specify the (I)nterface, since <a href="https://www.google.co.uk/search?q=link-local&oq=link-local&aqs=chrome..69i57j0l5.5567j0j7&sourceid=chrome&ie=UTF-8">link-local</a> addresses are not routable. 


[post-content post_name=rhsca-quiz] 

[accordion]

[toggle title="What are the main steps to enabling a IPv6 for the device enp0s8?"]
1. Create a new connection for this device.
2. manually assign an IPv4 IP address
3. Completely disable dhcp for ipv4. 
4. manually assign an IPv6 IP address
5. Completely disable dhcp for ipv6. 
6. Activate the new connection
7. Then test the connection using the ping and ping6 commands
[/toggle]
[toggle title="What is the command to create a new connection called codingbee-enp0s8 for the device enp0s8?"]
$ nmcli connection add con-name codingbee-enp0s8 type ethernet ifname enp0s8
[/toggle]
[toggle title="What is the command to assign IP address of 192.168.122.10/24 to codingbee-enp0s8 and how do you prevent dhcp from over-writing it?"]
$ nmcli connection modify codingbee-enp0s8 ipv4.addresses 192.168.122.10/24
$ nmcli connection modify codingbee-enp0s8 ipv4.method manual
[/toggle]
[toggle title="What is the command to assign IPv6 address of 2001:db8:85a3:0:0:8a2e:370:7334/64 to codingbee-enp0s8 and how do you prevent dhcp from over-writing it??"]
$ nmcli connection modify codingbee-enp0s8 ipv6.addresses 2001:db8:85a3:0:0:8a2e:370:7334/64
$ nmcli connection modify codingbee-enp0s8 ipv6.method manual
[/toggle]
[toggle title="What is the command to activate the new codingbee-enp0s8 connection?"]
$ nmcli connection up codingbee-enp0s8
[/toggle]
[toggle title="What commands do you need to run to test this new connection?"]
$ ping -c 3 192.168.56.103
$ ping6 -c 3 -I enp0s8 2001:db8:85a3:0:0:8a2e:370:7334
[/toggle]
[/accordion]