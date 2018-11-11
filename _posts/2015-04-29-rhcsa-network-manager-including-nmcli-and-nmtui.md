---
ID: 365
post_title: 'Network Manager &#8211; Getting acquainted with nmcli'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-network-manager-including-nmcli-and-nmtui
published: true
post_date: 2015-04-29 00:00:00
---
Network Manager is a software that's designed to manage your machine's network setup. At it's core, Network Manager works with the NetworkManager daemon:


<pre>
systemctl status NetworkManager
● NetworkManager.service - Network Manager
   Loaded: loaded (/usr/lib/systemd/system/NetworkManager.service; enabled; vendor preset: enabled)
   Active: active (running) since Sat 2018-03-31 13:57:38 UTC; 1h 5min ago
     Docs: man:NetworkManager(8)
 Main PID: 2859 (NetworkManager)
   CGroup: /system.slice/NetworkManager.service
           ├─2859 /usr/sbin/NetworkManager --no-daemon
           ├─2871 /sbin/dhclient -d -q -sf /usr/libexec/nm-dhcp-helper -pf /var/run/dhclient-enp0s8.pid -lf /var/lib/NetworkManager/dhclient-00cb8299-feb9-55b6-a378-3fdc720e0bc6-enp0s8.lease -cf /var/l...
           ├─2873 /sbin/dhclient -d -q -sf /usr/libexec/nm-dhcp-helper -pf /var/run/dhclient-enp0s9.pid -lf /var/lib/NetworkManager/dhclient-93d13955-e9e2-a6bd-df73-12e3c747f122-enp0s9.lease -cf /var/l...
           └─2877 /sbin/dhclient -d -q -sf /usr/libexec/nm-dhcp-helper -pf /var/run/dhclient-enp0s3.pid -lf /var/lib/NetworkManager/dhclient-493aa0d1-97e4-4f50-aa82-38802999373e-enp0s3.lease -cf /var/l...
</pre>

You can interact and send instructions to this daemon via various tools. All these tools do the same work:


<ul>
	<li>nmcli command - this the main tool we'll use</li>
	<li>nmtui - terminal based ui.</li>
	<li>ghome base gui tool</li>
</ul>


You use these tools to interact with the NetworkManager which in turn does various task including crud of config files, and managing the network daemon. 


One of the main responsibilities of NetworkManager is to configure+manage the network interfaces:

<pre>
$ ip address show
1: <strong>lo</strong>: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: <strong>enp0s3</strong>: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 08:00:27:ed:e3:61 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic enp0s3
       valid_lft 80625sec preferred_lft 80625sec
    inet6 fe80::2df4:9d63:ef73:6832/64 scope link
       valid_lft forever preferred_lft forever
3: <strong>enp0s8</strong>: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN qlen 1000
    link/ether 08:00:27:8b:ce:9e brd ff:ff:ff:ff:ff:ff
    inet 172.28.128.4/24 brd 172.28.128.255 scope global dynamic enp0s8
       valid_lft 635sec preferred_lft 635sec
    inet6 fe80::a00:27ff:fe8b:ce9e/64 scope link
       valid_lft forever preferred_lft forever
4: <strong>enp0s9</strong>: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN qlen 1000
    link/ether 08:00:27:0c:24:df brd ff:ff:ff:ff:ff:ff
    inet 172.28.128.3/24 brd 172.28.128.255 scope global dynamic enp0s9
       valid_lft 1112sec preferred_lft 1112sec
    inet6 fe80::a00:27ff:fe0c:24df/64 scope link
       valid_lft forever preferred_lft forever
</pre>



Each of these interfaces, have a corresponding config file which are located in the <code>/etc/sysconfig/network-scripts</code> folder:

<pre>
$ ll /etc/sysconfig/network-scripts | grep ifcfg
-rw-r--r--. 1 root    root      282 Mar 31 13:57 ifcfg-enp0s3
-rw-------. 1 vagrant vagrant   162 Mar 31 13:57 ifcfg-enp0s8
-rw-------. 1 vagrant vagrant   162 Mar 31 13:57 ifcfg-enp0s9
-rw-r--r--. 1 root    root      254 May  3  2017 ifcfg-lo
</pre>

These ifcfg-* files are often to referred to as <strong>connections</strong>. Here's what one looks like:


<pre>
$ cat /etc/sysconfig/network-scripts/ifcfg-enp0s8
BOOTPROTO=dhcp
ONBOOT=yes
DEVICE=enp0s8
NM_CONTROLLED=yes
</pre>



The content of some of these connections files are managed by Network Manager, to check which connections are managed by NM, we do:

<pre>
$ nmcli connection show
NAME           UUID                                  TYPE            DEVICE
System enp0s8  00cb8299-feb9-55b6-a378-3fdc720e0bc6  802-3-ethernet  enp0s8
System enp0s9  93d13955-e9e2-a6bd-df73-12e3c747f122  802-3-ethernet  enp0s9
enp0s3         493aa0d1-97e4-4f50-aa82-38802999373e  802-3-ethernet  enp0s3 
</pre>

Tip: nmcli commands can get quite long and difficult to remember, but luckily you can use the tab+tab autocomplete feature to make it much easier to use. 

Here we can see which connections NM manages along with which network interface (device) each connection is associated to. We can also see which devices are active:


<pre>
$ nmcli device status
DEVICE  TYPE      STATE      CONNECTION
enp0s3  ethernet  connected  enp0s3
enp0s8  ethernet  connected  System enp0s8
enp0s9  ethernet  connected  System enp0s9
lo      loopback  unmanaged  --
</pre>



Here we can see that we don't managed the loopback (lo) interface, meaning that there may be a ifcfg-lo connection available, but NM isn't managing that interface, and consequently isn't managing that lo device. 

One of the benefits of this connection-device relationship is that you can have multiple connections for a given device, and then restart the device to use a different connection. That way you can have multiple variations of connections for a given device. It's also a way to test out changes to connection before making them permanent. For example let's say we want to change a connections name of 'System enp0s8', first create a copy of the existing connection, which you can do like this:

<pre>
$ cp /etc/sysconfig/network-scripts/ifcfg-enp0s8 /etc/sysconfig/network-scripts/ifcfg-enp0s8-v2
</pre>

However NM won't notice this file until you restart the NM daemon. So better way to do this is via nmcli:


$ nmcli connection clone System\ enp0s8 enp0s8-v2
System enp0s8 (00cb8299-feb9-55b6-a378-3fdc720e0bc6) cloned as enp0s8-v2 (94e30eab-1c55-4021-8a1b-970c5e1826b3).


This ends up creating the new connection:

<pre>
$ cat /etc/sysconfig/network-scripts/ifcfg-enp0s8-v2
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=dhcp
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=no
RES_OPTIONS=single-request-reopen
NAME=enp0s8-v2
UUID=94e30eab-1c55-4021-8a1b-970c5e1826b3
DEVICE=enp0s8
ONBOOT=yes
</pre>

Now we can activate this connection:

<pre>
$ nmcli connection up enp0s8-v2
Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/6)
</pre>



Here are the connection (config files) that NM is actively using

<pre>
$ nmcli connection show
NAME           UUID                                  TYPE            DEVICE
enp0s8         7607287c-31fd-4cb3-b663-a4e4120ef265  802-3-ethernet  --
System enp0s3  ad2de184-098f-4eb8-92af-3e6c086f1186  802-3-ethernet  enp0s3
</pre>

Here, the enp0s3 device is using the connection called "System enp0s3", which corresponds to:

<pre>
$ cat /etc/sysconfig/network-scripts/ifcfg-enp0s3 | grep NAME
NAME="System enp0s3"
</pre>

To create a new connection, we do:


<pre>

$ nmcli connection add con-name "CustCon" ifname enp0s3 type ethernet autoconnect no
Connection 'CustCon' (4088f445-74b7-4f75-a8c1-513d13392ff8) successfully added.
</pre>

Running this command ended up creating the following new connection:

<pre>
$ ls -l /etc/sysconfig/network-scripts/ifcfg-CustCon              
-rw-r--r--. 1 root root 278 Oct  3 18:58 /etc/sysconfig/network-scripts/ifcfg-CustCon
</pre>

The contents of this connedction file is:

<pre>
$ cat ifcfg-CustCon
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
NAME=CustCon
UUID=991d8f70-7f92-481e-9202-c7afe5a434c0
DEVICE=enp0s3
ONBOOT=no

</pre>

As you can see, you can have more than one connection per interface and switch between them. 

Next we can see the available connections:

<pre>
$ nmcli connection show
NAME           UUID                                  TYPE            DEVICE
CustCon        4088f445-74b7-4f75-a8c1-513d13392ff8  802-3-ethernet  --
enp0s8         7607287c-31fd-4cb3-b663-a4e4120ef265  802-3-ethernet  --
System enp0s3  ad2de184-098f-4eb8-92af-3e6c086f1186  802-3-ethernet  enp0s3
</pre>

As you can see, our new connection is not attached to a device, i.e it is not currently being used. 

To switch between connections, you do:

<pre>
$ nmcli connection down System\ enp0s3
Connection 'System enp0s3' successfully deactivated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/5)
</pre>

This brings down the network session:

<pre>
$ ip addr show enp0s3
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 08:00:27:d4:f9:99 brd ff:ff:ff:ff:ff:ff
$ nmcli device status
DEVICE  TYPE      STATE         CONNECTION
enp0s3  ethernet  disconnected  --
enp0s8  ethernet  unmanaged     --
lo      loopback  unmanaged     --

</pre>

Next we bring up the network, but this time with the new connection:
<pre>
$ nmcli connection up CustCon
Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/4)

</pre>

Now we can check that our new connection is now active:

<pre>
$ nmcli connection show
NAME           UUID                                  TYPE            DEVICE
enp0s8         7607287c-31fd-4cb3-b663-a4e4120ef265  802-3-ethernet  --
System enp0s3  ad2de184-098f-4eb8-92af-3e6c086f1186  802-3-ethernet  --
CustCon        991d8f70-7f92-481e-9202-c7afe5a434c0  802-3-ethernet  enp0s3
enp0s3-static  0d936343-5c8e-4545-9174-f295b6c9ae86  802-3-ethernet  --

</pre>

The main purpose of this approach is that it lets you try/use/experiment various connection settings without losing the original.  

Here's the nmcli help info:


<pre>
$ nmcli -h
Usage: nmcli [OPTIONS] OBJECT { COMMAND | help }

OPTIONS
  -t[erse]                                   terse output
  -p[retty]                                  pretty output
  -m[ode] tabular|multiline                  output mode
  -f[ields] <field1,field2,...>|all|common   specify fields to output
  -e[scape] yes|no                           escape columns separators in values
  -n[ocheck]                                 don't check nmcli and NetworkManager versions
  -a[sk]                                     ask for missing parameters
  -w[ait] <seconds>                          set timeout waiting for finishing operations
  -v[ersion]                                 show program version
  -h[elp]                                    print this help

OBJECT
  g[eneral]       NetworkManager's general status and operations
  n[etworking]    overall networking control
  r[adio]         NetworkManager radio switches
  c[onnection]    NetworkManager's connections
  d[evice]        devices managed by NetworkManager
  a[gent]         NetworkManager secret agent or polkit agent
</pre>

As you can see, nmcli is actually a high-level command that is used to access a bunch of subcommands (aka objects).


For example nmcli commands, check out:

<pre>
$ man nmcli-examples
</pre>

<h2>
Network Manager Terminal User Interface nmtui
</h2>
This tool is another way of configuring your network settings. This tool is similar to what the Network Manager gnome gui does.