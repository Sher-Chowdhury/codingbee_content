---
ID: 2253
post_title: >
  Routing network traffic and configuring
  static routes on CentOS/RHEL 7
author: sher
post_excerpt: >
  A CentOS machine can be set set up to
  act as router, i.e. it can be used
  facilitate the communication between two
  or more devices.
layout: post
permalink: >
  https://codingbee.net/tutorials/rhce/routing-network-traffic-and-configuring-static-routes-on-centos-rhel-7
published: true
post_date: 2017-08-03 20:29:04
---
A CentOS machine can be set set up to act as router, i.e. it can be used facilitate the communication between two or more devices.To understand how this works, we'll use the following example: 


<pre style="font-size:16px;line-height:100%;">

       box1                       routingvm                        box2
+---------------------+      +----------------------+      +-------------------+
|                     |      |                      |      |                   |
|                     |      |                      |      |                   |
|  +---------------+  |      |    +---------------+ |      |                   |
|  |enp0s8:        |<-----------> |enp0s8:        | |      |                   |
|  |192.168.10.101 |  |      |    |192.168.10.100 | |      |                   |
|  |255.255.255.0  |  |      |    |255.255.255.0  | |      |                   |
|  +---------------+  |      |    +---------------+ |      |                   |
|                     |      |                      |      |                   |
|                     |      |                      |      |                   |
|                     |      |    +---------------+ |      |  +--------------+ |
|                     |      |    |enp0s9:        | |      |  | enp0s8:      | |
|                     |      |    |10.0.0.10      |<--------->| 10.0.0.11    | |
|                     |      |    |255.255.255.0  | |      |  | 255.255.255.0| |
|                     |      |    +---------------+ |      |  +--------------+ |
|                     |      |                      |      |                   |
|   +------------+    |      |    +-----------+     |      |  +----------+     |
|   |enp0s3      |    |      |    |enp0s3     |     |      |  | enp0s3   |     |
|   +-----+------+    |      |    +----+------+     |      |  +-----+----+     |
|         |           |      |         |            |      |        |          |
+---------------------+      +----------------------+      +-------------------+
          |                            |                            |
          v                            v                            v
+------------------------------------------------------------------------------+
|                        router (e.g. cisco router)                            |
|                                 (10.0.2.2)                                   |
+------------------------------------------------------------------------------+
                                       |
                                       v
+------------------------------------------------------------------------------+
|                                   INTERNET                                   |
|                                                                              |
+------------------------------------------------------------------------------+
</pre>

You can follow along with this example using the <a href="https://github.com/Sher-Chowdhury/cento7-static-routing-demo" rel="nofollow">cento7-static-routing-demo vagrant project on github</a>.  

In this diagram, box1, routingvm, and box2 are all CentOS machines. The goal here is to get box1 to successfully ping box2. However that box1 and box2 are on different private networks which means that there's no direct route from box1 to box2. This means if we did try to ping box2 it will fail:
<pre>
[root@box1 ~]# ping -c 3 10.0.0.11
PING 10.0.0.11 (10.0.0.11) 56(84) bytes of data.

--- 10.0.0.11 ping statistics ---
3 packets transmitted, 0 received, 100% packet loss, time 2001ms
</pre> 

However routingvm happens to be a member of both private networks, so it can potentially be configured to facilitate communications, so that it can forward requests from box1 to box2.  

Note: All three CentOS boxes can reach the internet, via their respective enp0s3 network adapters. the enp0s3 device are designated for internet access only, so it won't be possible to ping box2 via the internet, unless box2's enp0s3 box has a dedicated public ip address attached to them. We'll assume that isn't the case, so let's ignore all enp0s3 network adapters. 


In the above setup there are 2 private internal networks, where: 


<ul>
	<li>box1 (via the enp0s8 network) belongs to a private internal network, 192.168.10.0/24. </li>
	<li>box2 (via the enp0s8 network) belongs to a private internal network, 10.0.0.0/24.</li>
	<li>routingvm is a member of both of these private internal networks. It is a member of the 192.168.10.0/24, via the enp0s8 network adapter, and it is a member of 10.0.0.0/24 via the enp0s9 network adapter.</li>
</ul>


This means that routingvm can ping to both box1 and box2:

<pre>
# pinging box1
[root@routingvm ~]# ping -c 3 192.168.10.101
PING 192.168.10.101 (192.168.10.101) 56(84) bytes of data.
64 bytes from 192.168.10.101: icmp_seq=1 ttl=64 time=0.268 ms
64 bytes from 192.168.10.101: icmp_seq=2 ttl=64 time=0.438 ms
64 bytes from 192.168.10.101: icmp_seq=3 ttl=64 time=0.306 ms

--- 192.168.10.101 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2001ms
rtt min/avg/max/mdev = 0.268/0.337/0.438/0.074 ms

# pinging box2
[root@routingvm ~]# ping -c 3 10.0.0.11
PING 10.0.0.11 (10.0.0.11) 56(84) bytes of data.
64 bytes from 10.0.0.11: icmp_seq=1 ttl=64 time=0.280 ms
64 bytes from 10.0.0.11: icmp_seq=2 ttl=64 time=0.317 ms
64 bytes from 10.0.0.11: icmp_seq=3 ttl=64 time=0.300 ms

--- 10.0.0.11 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2000ms
rtt min/avg/max/mdev = 0.280/0.299/0.317/0.015 ms

</pre> 

Unfortunately, the above output doesn't show which interface the ping command used. But you can get that info using the ip command:

<pre>
# routing info for box1
[root@routingvm ~]# ip route get 192.168.10.101
192.168.10.101 dev enp0s8  src 192.168.10.100
    cache

# routing info for box2
[root@routingvm ~]# ip route get 10.0.0.11
10.0.0.11 dev enp0s9  src 10.0.0.10
    cache
</pre>

Also here's the route table for routingvm:

<pre>
[root@routingvm ~]# ip route list
default via 10.0.2.2 dev enp0s3  proto static  metric 100
10.0.0.0/24 dev enp0s9  proto kernel  scope link  src 10.0.0.10  metric 100
10.0.2.0/24 dev enp0s3  proto kernel  scope link  src 10.0.2.15  metric 100
192.168.10.0/24 dev enp0s8  proto kernel  scope link  src 192.168.10.100  metric 100
</pre>


box1 and box2 can both ping routingvm:


<pre>
[root@box1 ~]# ping -c 3 192.168.10.100
PING 192.168.10.100 (192.168.10.100) 56(84) bytes of data.
64 bytes from 192.168.10.100: icmp_seq=1 ttl=64 time=0.246 ms
64 bytes from 192.168.10.100: icmp_seq=2 ttl=64 time=0.272 ms
64 bytes from 192.168.10.100: icmp_seq=3 ttl=64 time=0.314 ms

--- 192.168.10.100 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 1999ms
rtt min/avg/max/mdev = 0.246/0.277/0.314/0.031 ms


[root@box1 ~]# ip route get 192.168.10.100
192.168.10.100 dev enp0s8  src 192.168.10.101
    cache


[root@box1 ~]# ip route list
default via 10.0.2.2 dev enp0s3  proto static  metric 100
10.0.2.0/24 dev enp0s3  proto kernel  scope link  src 10.0.2.15  metric 100
192.168.10.0/24 dev enp0s8  proto kernel  scope link  src 192.168.10.101  metric 100
</pre>


Similarly for box2: 


<pre>
[root@box2 ~]# ping -c 3 10.0.0.10
PING 10.0.0.10 (10.0.0.10) 56(84) bytes of data.
64 bytes from 10.0.0.10: icmp_seq=1 ttl=64 time=0.375 ms
64 bytes from 10.0.0.10: icmp_seq=2 ttl=64 time=0.478 ms
64 bytes from 10.0.0.10: icmp_seq=3 ttl=64 time=0.325 ms

--- 10.0.0.10 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2000ms
rtt min/avg/max/mdev = 0.325/0.392/0.478/0.067 ms


[root@box2 ~]# ip route get 10.0.0.10
10.0.0.10 dev enp0s8  src 10.0.0.11
    cache


[root@box2 ~]# ip route list
default via 10.0.2.2 dev enp0s3  proto static  metric 100
10.0.0.0/24 dev enp0s8  proto kernel  scope link  src 10.0.0.11  metric 100
10.0.2.0/24 dev enp0s3  proto kernel  scope link  src 10.0.2.15  metric 100
</pre>

Now since routingvm can freely communicate with both box1 and box2, it means that routingvm can be used to facilitate network traffic between box1 and box2. To do this, you first need to configure the box1 network settings so that any traffic that is destined for the 10.0.0.0/24 network needs to sent via box1's enp0s8 interface to routingvm. This is done by running the following command to add a new rule to box1's route table:


<pre>
[root@box1 ~]# ip route add 10.0.0.0/24 via 192.168.10.100 dev enp0s8
</pre>
Note: you can undo the above command by running it again, but with 'add' replaced with 'del'

This ends up adding the following entry to the routing table:

<pre>
[root@box1 ~]# ip route list
default via 10.0.2.2 dev enp0s3  proto static  metric 100
<strong>10.0.0.0/24 via 192.168.10.100 dev enp0s8</strong>
10.0.2.0/24 dev enp0s3  proto kernel  scope link  src 10.0.2.15  metric 100
192.168.10.0/24 dev enp0s8  proto kernel  scope link  src 192.168.10.101  metric 100
</pre>


This line basically says, any traffic that is destined to an ip address that falls in the range 10.0.0.1-10.0.0.254, must be sent out via enp0s8 to the ip 192.168.10.100.However this won't persistant, there are 3 ways to make it persistant (all of which requires restarting the network daemon), the first approach is to create this file


<pre>
[root@box1 ~]# cat /etc/sysconfig/network-scripts/route-enp0s8
10.0.0.0/24 via 192.168.10.100
</pre>

You will need to create this file if it doesn't already exist. The second approach is to get the route-enp0s8 file created for you by using the nmcli command:

<pre>
$ nmcli connection modify System\ enp0s8 +ipv4.routes "10.0.0.0/24 192.168.10.100""
</pre>

The third approach is to create the following file with the content:

<pre>
$ cat /etc/sysconfig/static-routes
any net 10.0.0.0 netmask 255.255.255.0 gw 192.168.10.100 dev enp0s8
</pre>
The <strong>/etc/sysconfig/static-routes</strong> is specifically mentioned in the network init script: /etc/init.d/network. After taking one of these 3 approaches, you need to restart the network daemon to load this in:


<pre>
$ systemctl restart network
</pre>

You can see the new rule by running:

<pre>
[root@box1 ~]# ip route list
default via 10.0.2.2 dev enp0s3  proto static  metric 100
<strong>10.0.0.0/24 via 192.168.10.100 dev enp0s8</strong>
10.0.2.0/24 dev enp0s3  proto kernel  scope link  src 10.0.2.15  metric 100
192.168.10.0/24 dev enp0s8  proto kernel  scope link  src 192.168.10.101  metric 100
</pre>

At this point we have now configured box1 to send box2-destined traffic to routingvm. But if we run:

<pre>
[root@box1 ~]# traceroute 10.0.0.11
traceroute to 10.0.0.11 (10.0.0.11), 30 hops max, 60 byte packets
 1  * * *
 2  * * *
 3  * * *
 4  * * *
</pre>

This still doesn't look good. That's because we need to configure routingvm to start acting as a router. To do this we need to first configure routingvm's kernel setting to allow ip forwarding. First we check if it is currently enabled:


<pre>
[root@routingvm ~]# sysctl net.ipv4.ip_forward
net.ipv4.ip_forward = 0
</pre>
 
In this case, it isn't, so we enable it:

<pre>
[root@routingvm ~]# echo 'net.ipv4.ip_forward=1' >> /etc/sysctl.conf
[root@routingvm ~]# sysctl --system   # this is to reload config files.
</pre>

Then check to confirm it has worked:

<pre>
[root@routingvm ~]# sysctl net.ipv4.ip_forward
net.ipv4.ip_forward = 1
</pre>


Now if we run traceroute on box1 again, we get:

<pre>
[root@box1 ~]# traceroute 10.0.0.11
traceroute to 10.0.0.11 (10.0.0.11), 30 hops max, 60 byte packets
 1  192.168.10.100 (192.168.10.100)  0.552 ms  0.506 ms  0.492 ms^C
</pre>

This indicates that communication between box1 and routingvm is now working. However routingvm isn't configured to forwarding on requests to box2 yet, so pinging is still failing:


<pre>
[root@box1 ~]# ping -c3 10.0.0.11
PING 10.0.0.11 (10.0.0.11) 56(84) bytes of data.

--- 10.0.0.11 ping statistics ---
3 packets transmitted, 0 received, 100% packet loss, time 2000ms
</pre>



Next we start firewalld service. We need to use firewalld becuase it has a key feature for rerouting traffic, called 'masquerade':


<pre>
[root@routingvm ~]# systemctl start firewalld
[root@routingvm ~]# systemctl status firewalld
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; disabled; vendor preset: enabled)
   Active: active (running) since Sat 2017-08-05 23:19:40 UTC; 2s ago
     Docs: man:firewalld(1)
 Main PID: 5925 (firewalld)
   CGroup: /system.slice/firewalld.service
           └─5925 /usr/bin/python -Es /usr/sbin/firewalld --nofork --nopid

Aug 05 23:19:40 routingvm.local systemd[1]: Starting firewalld - dynamic firewall daemon...
Aug 05 23:19:40 routingvm.local systemd[1]: Started firewalld - dynamic firewall daemon.
</pre>

Finally we enabling the masquerade feature, reload the firewalld configs, and then confirm that it is enabled:


<pre>
[root@routingvm ~]# firewall-cmd --permanent --add-masquerade
success
[root@routingvm ~]# firewall-cmd --reload
success
[root@routingvm ~]# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8 enp0s9
  sources:
  services: dhcpv6-client ssh
  ports:
  protocols:
  masquerade: yes
  forward-ports:
  sourceports:
  icmp-blocks:
  rich rules:
</pre>


Enabling masquerade basically means that firewall will actively forward on any incoming network traffic that is destined for somewhere else. That's now everything that's required for configuring routingvm. We now go back to box1 and do another ping test:


<pre>
[root@box1 ~]# ping -c 3 10.0.0.11
PING 10.0.0.11 (10.0.0.11) 56(84) bytes of data.
64 bytes from 10.0.0.11: icmp_seq=1 ttl=63 time=0.543 ms
64 bytes from 10.0.0.11: icmp_seq=2 ttl=63 time=0.821 ms
64 bytes from 10.0.0.11: icmp_seq=3 ttl=63 time=0.664 ms

--- 10.0.0.11 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2001ms
rtt min/avg/max/mdev = 0.543/0.676/0.821/0.113 ms
</pre>

Another test we can do is try to ssh into box2 from box1:


<pre>
[root@box1 ~]# ssh 10.0.0.11
The authenticity of host '10.0.0.11 (10.0.0.11)' can't be established.
ECDSA key fingerprint is 25:f0:05:ec:12:ce:9f:33:ff:38:58:31:ad:e2:4d:bd.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '10.0.0.11' (ECDSA) to the list of known hosts.
root@10.0.0.11's password:
Last login: Sat Aug  5 15:56:05 2017
[root@box2 ~]#
</pre>
    

Finally we can confirm that the traffic is going via routingvm by using the traceroute command:


<pre>
[root@box1 ~]# traceroute 10.0.0.11
traceroute to 10.0.0.11 (10.0.0.11), 30 hops max, 60 byte packets
 1  192.168.10.100 (192.168.10.100)  0.306 ms  0.235 ms  0.146 ms
 2  10.0.0.11 (10.0.0.11)  0.853 ms  0.810 ms  0.728 ms
</pre>

<h4>Recommend video courses</h4>

https://app.pluralsight.com/player?course=lfcs-linux-networking&author=andrew-mallett&name=lfcs-linux-networking-m6&clip=3&mode=live

For masquarading: 

https://app.pluralsight.com/player?course=security-network-host-lfce&author=anthony-nocentino&name=security-network-host-lfce-m4&clip=8&mode=live


[post-content post_name=rhsca-quiz] 

The questions relates to the diagram at the top of this article. 

[accordion]
[toggle title="What changes needs to be done on all three boxes"]
box1: 
- setup persistant static route
- test route works once everything has been setup
routingvm:
- update sysctl settings to allow traffic forwarding
- enable firewalld feature for routing traffic
box 2:
- no changes needs to be made. 
[/toggle]
[toggle title="What command do you run to check which network interface was used for issuing a ping to 192.168.10.101?"]
$ ip route get 192.168.10.101
192.168.10.101 dev enp0s8  src 192.168.10.100
    cache
[/toggle]
[toggle title="What is the command to display the routing table?"]
$ ip route list
[/toggle]
[toggle title="What is the command to non-persistantly add a routing rule, where any traffic destined for the ip range 10.0.0.0/24 get's forwarded to the ip address 192.168.10.100 using the enp0s8 interface?"]
$ ip route add 10.0.0.0/24 via 192.168.10.100 dev enp0s8
[/toggle]
[toggle title="What is the command to make this persistant?"]
$ echo '10.0.0.0/24 via 192.168.10.100' >> /etc/sysconfig/network-scripts/route-enp0s8
$ systemctl restart network
[/toggle]
[toggle title="What commands do you run to persistant update the kernel settings?"]
[root@routingvm ~]# echo 'net.ipv4.ip_forward=1' >> /etc/sysctl.conf
[root@routingvm ~]# sysctl --system   # this is to reload config files.
[/toggle]
[toggle title="What commands do you run to persistantly enable the firewalld masquarade mode?"]
$ firewall-cmd --permanent --add-masquerade
$ systemctl restart firewalld
[/toggle]
[toggle title="What tests do you do on box1 to confirm routing is working?"]
$ ping -c 3 10.0.0.11
$ ssh 10.0.0.11
$ traceroute 10.0.0.11
[/toggle]
[toggle title="What is the command to open the Network Manager gui interface?"]
$ nm-connection-editor      
# This approach is better, because for some reason I couldn't save any gui changes using the top right network icon. 
[/toggle]
[toggle title="question?"]
answer
[/toggle]
[/accordion]