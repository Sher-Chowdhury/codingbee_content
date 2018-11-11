---
ID: 366
post_title: 'RHCSA &#8211; The concept of default routes and DNS'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-the-concept-of-default-routes-and-dns
published: true
post_date: 2015-04-30 00:00:00
---
Let's say we have 2 networks:

<a href="http://codingbee.net/wp-content/uploads/2015/04/IMG_20150501_1954472.jpg"><img class="alignnone size-full wp-image-4135" src="http://codingbee.net/wp-content/uploads/2015/04/IMG_20150501_1954472.jpg" alt="IMG_20150501_195447~2" width="1944" height="1931" /></a>Let's say we have two networks,

Now the ip ranges of each network is:

Network 1 - 192.168.0.1/28
Network 2 - 10.0.0.1/28

One of the devices in network 1 (i.e. 192.168.0.1/82.2.140.10) is connected directly to the internet. This device has 2 interfaces, hence it has 2 ip address, one facing off to the internet (192.168.0.1), and the other to network 1 (82.2.140.10).

Therefore if any other machines in network-1 wants to send out data to the internet, then it has to do so via 192.168.0.1. Hence all other network-1 devices needs to be configured so that it's default gateway setting is set to 192.168.0.1. The default gateway device then forwards the data on. 

However for network-2, there are no devices that are connected directly to the internet. However one device, 192.168.0.150/10.0.0.1, is a device that is a member of both networks. One interface is facing off to network1 and the other to network2.

Therefore it is possible of a network-2 device to indirectly send data to the internet via 10.0.0.1, which in turn forwards it to 192.168.0.1.   

In order for this to work, the default gateway for 10.0.0.25 should be set to 10.0.0.1. 

Note, there is a rule, which is that the default gateway ip address must be in the same ip range as all the other devices in the network. 

Now what if 192.168.0.05 wants to forward data to 1.0.0.25. In this case, it has be done 10.0.0.1. Hence for this to be possible, we need to set up a second "route" whereby if the target ip address is in the network2 range, then it will forward the data to 192.168.0.150 rather than the default gateway (192.168.0.1). 

You can temporarily add this route using the "ip" command. First we check the current routing info 192.168.0.5:

<pre>
$ ip route show
default via 192.168.1.1 dev enp0s3  proto static  metric 100
192.168.0.1/24 dev enp0s3  proto kernel  scope link  src 192.168.0.5  metric 100

</pre>


Now we add the new route:

<pre>
$ ip route add 10.0.0.0/28 via 192.168.1.150
</pre> 

Note, the above doesn't give any outputs. 

Now to check this has worked we do:


<pre>
$ ip route show
default via 192.168.1.1 dev enp0s3  proto static  metric 100
<strong>10.0.0.0/28 via 192.168.1.150 dev enp0s3</strong>
192.168.1.0/24 dev enp0s3  proto kernel  scope link  src 192.168.1.124  metric 100
</pre>


However for the rhcsa exam, you are only required to know how to change the default gateway. Which you can simply do by editing the relevant network-scripts. 


<h2>Domain Name Servers (DNS)</h2>

These are servers that just contains a list of friendly machine fqdn/hostnames and what ip-number they are mapped to. Once again you can specify what your dns server is in your network-scripts. 


A domain server can reside anywhere, i.e. inside a local network or outside on the internet.


 

<strong>Also see:
</strong>



A machine can be part of more than one network. e.g. eth0 interface can be linked to LAN A, whereas eth1 can be connected to “LAN B”. In these scenarios, the machine needs to know which network outgoing traffic needs to be sent to. By definition, LAN A and B will have different IP ranges, and as a result, you can specify which interface to use for a given range of IP addresses.

<a href="http://codingbee.net/wp-content/uploads/2014/05/Network-coomands.png"><img class="alignnone size-full wp-image-84" src="http://codingbee.net/wp-content/uploads/2014/05/Network-coomands.png" alt="Network-coomands" width="717" height="193" /></a>

This routing table contains several columns, column 1 (destinations) indicates the IP range, and column 3 (Genmask) helps to define that range more accurately.

&nbsp;

If column 1, contains the entry “default” (which also would mean that the “G” flag is enabled), then it means that it is the default gateway.

&nbsp;

If the IP falls outside the LAN A and B range, then you could specify the routing for the default gateway, and all out-of-range outbound traffic will be sent to that destination. This tends to be the router’s ip address, this is how it is done:


In the routing table, wherever there is an asterisk, it means that data is being redirected within the LAN.


<strong>Must survive reboot:</strong>

ifdown {interface name} boot # switches off an interface and ensures that it doesn’t come back on during reboot.

ifup {interface name}     boot # brings interface back up again (i.e. connects interface to internet). And ensures it

# automatically connects again after a reboot.


<strong>Book ref:</strong>

page 38, chapter 3 - good technique on how to use ping effectively.

page 40, chapter 3 - this list the various ifconfig options.

<strong>Study guide ref:</strong>

Inserttexthere

<strong>Need to learn more about:</strong>

arp command - bottom of page 40, chapter 3. I think this associates an IP address to a mac address.

<a href="http://www.cyberciti.biz/faq/linux-setup-default-gateway-with-route-command/">http://www.cyberciti.biz/faq/linux-setup-default-gateway-with-route-command/</a>

<a href="http://answers.yahoo.com/question/index?qid=20081006214718AAlmOAZ">http://answers.yahoo.com/question/index?qid=20081006214718AAlmOAZ</a> - this explain what the “RX” and “TX” values means in the ifconfig data.

<a href="http://www.cyberciti.biz/faq/centos-add-route-command/">http://www.cyberciti.biz/faq/centos-add-route-command/</a>