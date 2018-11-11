---
ID: 361
post_title: 'RHCSA &#8211; The &#8220;ip&#8221; command'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-the-ip-command
published: true
post_date: 2015-04-18 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is the command to display all your interfaces along with their addressing info?"]
$ ip addr show
[/toggle]
[toggle title="What is the command to show routing info along with the default gateway?"]
$ ip route show
[/toggle]
[toggle title="What is the command to show network traffic?"]
$ ip -s link show
[/toggle]
[toggle title="What is the command?"]


[/toggle]
[toggle title="What is the command?"]


[/toggle]
[toggle title="What is the command?"]


[/toggle]
[toggle title="What is the command?"]


[/toggle]
[toggle title="What is the command?"]


[/toggle]
[toggle title="What is the command?"]


[/toggle]
[toggle title="What is the command?"]


[/toggle]
[/accordion]

<hr/>





The ip command is mainly used for querying your network setup. The ip command is made up lots of sub commands (which you can display using the tab+tab technique). One commonly used subcommand is <code>ip addr show</code>. This shows current interfaces along with their addressing info:


<pre>
$ ip addr show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 08:00:27:82:83:fc brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.124/24 brd 192.168.1.255 scope global dynamic enp0s3
       valid_lft 81874sec preferred_lft 81874sec
    inet6 fe80::a00:27ff:fe82:83fc/64 scope link
       valid_lft forever preferred_lft forever
</pre>


This shows that our machine's ip number is 192.168.1.124 (and it is part of a network that has a subnet mask of 24). However an easier way to output your machine's ip number is:


Now let's view the current routing table:


<pre>
$ ip route show
default via 192.168.1.1 dev enp0s3  proto static  metric 100
192.168.1.0/24 dev enp0s3  proto kernel  scope link  src 192.168.1.124  metric 100
</pre>

Here the router ip address is 192.168.1.1, which is the default gateway. Anything that falls in the 192.168.1.0/24 subnet will use the 192.168.1.1 (i.e. the router) as the default gateway. 



Any outgoing data will automatically get directed to the default gateway, potentially through a series of switches. To improve performance you may want to add a new route which helps data to bypass switches and reaches it's destination faster. This is done by adding a new route, like this:

To view data transfer statistics we do:


<pre>
$ ip -s link show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    RX: bytes  packets  errors  dropped overrun mcast
    420        4        0       0       0       0
    TX: bytes  packets  errors  dropped carrier collsns
    420        4        0       0       0       0
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP mode DEFAULT qlen 1000
    link/ether 08:00:27:82:83:fc brd ff:ff:ff:ff:ff:ff
    RX: bytes  packets  errors  dropped overrun mcast
    704513     7732     0       0       0       120
    TX: bytes  packets  errors  dropped carrier collsns
    266335     1391     0       0       0       0

</pre>