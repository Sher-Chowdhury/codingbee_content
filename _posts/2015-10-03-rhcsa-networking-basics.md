---
ID: 460
post_title: Networking basics
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-networking-basics
published: true
post_date: 2015-10-03 00:00:00
---
<h2>Network Interfaces</h2>
Network interfaces, are physical hardware components inside your machine that allows it to communicate with the outside work. They come in various shapes and sizes, here's an example of one:

<img src="https://codingbee.net/wp-admin/admin-ajax.php?action=wpmf-dbxdownload-file&id=id%3AWl2OhL3IRwAAAAAAAAABLA&link=true&dl=1">

Here's another example:

<img src="https://codingbee.net/wp-admin/admin-ajax.php?action=wpmf-dbxdownload-file&id=id%3AWl2OhL3IRwAAAAAAAAABLQ&link=true&dl=1">

These interfaces also comes in virtual forms as well. 

You can view a list of these interfaces by running:


<pre>
[root@localhost ~]# ls -l /sys/class/net/
total 0
lrwxrwxrwx. 1 root root 0 Jul 15 12:12 enp0s3 -> ../../devices/pci0000:00/0000:00:03.0/net/enp0s3
lrwxrwxrwx. 1 root root 0 Jul 15 12:12 enp0s8 -> ../../devices/pci0000:00/0000:00:08.0/net/enp0s8
lrwxrwxrwx. 1 root root 0 Jul 15 12:12 enp0s9 -> ../../devices/pci0000:00/0000:00:09.0/net/enp0s9
lrwxrwxrwx. 1 root root 0 Jul 15 12:12 lo -> ../../devices/virtual/net/lo
lrwxrwxrwx. 1 root root 0 Jul 15 12:18 virbr0 -> ../../devices/virtual/net/virbr0
lrwxrwxrwx. 1 root root 0 Jul 15 12:18 virbr0-nic -> ../../devices/virtual/net/virbr0-nic
</pre>


Another way to list these devices is by using the 'ip' command:


<pre>
ip addr show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 08:00:27:d0:c3:19 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic enp0s3
       valid_lft 69115sec preferred_lft 69115sec
    inet6 fe80::7cc3:2fd9:d576:1b84/64 scope link
       valid_lft forever preferred_lft forever
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 08:00:27:45:e9:74 brd ff:ff:ff:ff:ff:ff
    inet 172.28.128.6/24 brd 172.28.128.255 scope global dynamic enp0s8
       valid_lft 962sec preferred_lft 962sec
    inet6 fe80::a00:27ff:fe45:e974/64 scope link
       valid_lft forever preferred_lft forever
4: enp0s9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 08:00:27:24:17:7b brd ff:ff:ff:ff:ff:ff
    inet 172.28.128.5/24 brd 172.28.128.255 scope global dynamic enp0s9
       valid_lft 1161sec preferred_lft 1161sec
    inet6 fe80::a00:27ff:fe24:177b/64 scope link
       valid_lft forever preferred_lft forever
5: virbr0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN qlen 1000
    link/ether 52:54:00:96:40:6a brd ff:ff:ff:ff:ff:ff
    inet 192.168.122.1/24 brd 192.168.122.255 scope global virbr0
       valid_lft forever preferred_lft forever
6: virbr0-nic: <BROADCAST,MULTICAST> mtu 1500 qdisc pfifo_fast master virbr0 state DOWN qlen 1000
    link/ether 52:54:00:96:40:6a brd ff:ff:ff:ff:ff:ff
</pre>

A third way to list these devices is by using nmcli:

<pre>
[root@localhost ~]# nmcli device status
DEVICE      TYPE      STATE      CONNECTION
virbr0      bridge    connected  virbr0
enp0s3      ethernet  connected  enp0s3
enp0s8      ethernet  connected  System enp0s8
enp0s9      ethernet  connected  System enp0s9
lo          loopback  unmanaged  --
virbr0-nic  tun       unmanaged  --
</pre>

We will cover nmcli later.


An <strong>interface</strong> is actually the slots on your physical network card. Some of these interfaces can be virtual slots. But ultimately they are like tunnels from your machine to the outside world.


These interfaces on their own don't know what connection config data to use, e.g. ip address, and that's where "connections" comes into play.  

<h2>Connections</h2>
A <strong>connection</strong> is actually a config file that contains information that an interface uses to connect to a network. All these connections files resides in the following directory, and their file name is in the form of ifcfg-{interface-name}:

<pre>
$ ls -l /etc/sysconfig/network-scripts | grep ifcfg
-rw-r--r--. 1 root root   318 Sep 16 19:31 ifcfg-enp0s3
-rw-r--r--. 1 root root   215 Oct  3 07:58 ifcfg-enp0s8
-rw-r--r--. 1 root root   254 Jan 15  2015 ifcfg-lo
</pre>

As you can see, each interface has it's own (connection) file. 

<h2>The Network service</h2>
There is a service called 'network':

<pre>
systemctl status network
● network.service - LSB: Bring up/down networking
   Loaded: loaded (/etc/rc.d/init.d/network; bad; vendor preset: disabled)
   Active: active (exited) since Sat 2017-07-15 12:12:10 UTC; 4h 59min ago
     Docs: man:systemd-sysv-generator(8)

Jul 15 12:12:10 localhost.localdomain systemd[1]: Starting LSB: Bring up/down networking...
Jul 15 12:12:10 localhost.localdomain network[863]: Bringing up loopback interface:  [  OK  ]
Jul 15 12:12:10 localhost.localdomain systemd[1]: Started LSB: Bring up/down networking.
</pre>

This daemon is responsible for running each interface. It runs each interface according to the information stored in the respective ifcfg-* files. 

<h2>Network Manager</h2>
Network Manager is essentially a service that monitors and controls what the network service does:

<pre>
$ systemctl status NetworkManager
NetworkManager.service - Network Manager
   Loaded: loaded (/usr/lib/systemd/system/NetworkManager.service; enabled)
   Active: active (running) since Sat 2015-10-17 00:12:08 BST; 2 weeks 0 days ago
 Main PID: 590 (NetworkManager)
   CGroup: /system.slice/NetworkManager.service
           ├─ 590 /usr/sbin/NetworkManager --no-daemon
           └─4595 /sbin/dhclient -d -q -sf /usr/libexec/nm-dhcp-helper -pf /var/run/dhclient-enp0s3.pid -lf /var/lib/NetworkManag...

Oct 31 18:31:14 puppetmaster.local NetworkManager[590]: <info>  (enp0s3): Activation: Stage 5 of 5 (IPv4 Commit) complete.
</pre>

You can interact with the NetworkManager daemon via a collection of gui, tui, and cli utilities. 

Also checkout:

<pre>$ man nm-settings</pre>