---
ID: 2090
post_title: Network Address Translation (NAT)
author: sher
post_excerpt: >
  This is gives a practical hands on
  walkthrough on understanding,
  configuring, and troubleshooting network
  related problems. We will be exclusively
  using CentOS throughout this series.
layout: post
permalink: >
  https://codingbee.net/tutorials/linux/networking/network-address-translation-nat
published: true
post_date: 2017-07-23 23:18:42
---
The world is running out of ipv4 based ip addresses. NAT has been introduced as a system the circumvents the need to assign every internet enabled device to have it's own dedicated public ip4 addresses. The way this is done is that devices that are connected to a local network (e.g. a laptop connected to a home router) is assigned with a <a href="https://codingbee.net/tutorials/linux/networking/public-and-private-ip-addresses">private ip address</a>. The only device that has a public ip address in this scenario is the router itself. All devices in the local network accesses the internet via the router. All data packets travelling through the router has there respective header section modified with additional internal mapping information that allows it to keep track of which data packet is associated with which device. 

Let's consider an example. Let's say we have 2 devices (e.g. a laptop and a computer) that are connected to the same private network. they will be assigned with private addresses (e.g. 192.168.0.10 and 192.168.0.50). let's say both devices tries to access twitter.com at the same time, then the outward requesting data packets from both devices goes to the router, the router then attaches a kind of a label to each data packet so that it knows which device each data packet is originated from, it then forwards the modified data packets to their destination, which in this case is the twitter.com. The twitter.com server processes the data packets and sends back data packets. The important thing here is that the returning data packets have the same label data attached to them. When the router retrieves the data packets, it then determines which device the data packet should be routed to, then forwards on the data. This label is referred to as an internal 'port' number. The router maintains an internal table that shows which port is attached to which port number.  

Another thing to note is that the data packet's that are sent out by the devices also contains the device's internal IP address. This information can't is only of use to the router, which swaps out the local private IP address and replaces it with the router's own public IP address before forwarding on to twitter.com. That's so that when twitter.com receives the data packets, it can then send responding data packets back to the router. When the router receives the data packets back from twitter.com, it takes a look at the label (i.e. the internal port number), then queries it's own mapping table to identify what the local IP address should be, then it swaps out it's public address and inserts the devices private IP addresses, then forwards the data packet it on to the device.  


There are 2 main types of NAT:

<ul>
	<li>Port Address Translation (PAT)</li>
	<li>Static NAT</li>
</ul>


<h4>Port Address Translation (PAT)</h4>
This type of NAT system is commonly used in home networks. The twitter example given above is actually an example of PAT in action. 

We can now elaborate on this example by seeing how this fits in with the OSI model. Basically, PAT ends up using the following two packet headers, when data is being sent outbound (i.e. from an internal device to the internet (e.g. twitter.com)): 

<ul>
	<li><strong>IP header</strong> - this packet header is attached by the internal device to data packets during the OSI's network layer.  The data packet's <a href="https://en.wikipedia.org/wiki/IPv4#Header" rel="nofollow">IP header structure</a> follows a certain format. In particular it stores information about the source IP address. The device then forwards the data packet to the internet, via the router. The router intercepts the data packet and then replaces the IP header's source IP info with the router's IP own public IP address, It then forwards the packets on to twitter.com. 
<br/>

<br/>
</li>
	<li><strong>TCP Header</strong> - this packet header is attached by the internal device to data packets during the OSI's transport layer. The data packet's <a href="https://en.wikipedia.org/wiki/Transmission_Control_Protocol#TCP_segment_structure" rel="nofollow">TCP header structure</a> follows a certain format. In particular it stores information about the source port number, the device is told by the router (during dhcp) what port number is used, it's usually a non-standard port number, e.g. 34323. This port number doesn't change during the outbound journey. In particular it stores information about the source port. The device then forwards the data packet to the internet, via the router. The router intercepts the data packet and then replaces the IP header's source IP info with the router's IP own public IP address, It then forwards the packets on to twitter.com. Note, the destination port is whatever whatever the desination device's ports are listening, e.g. port 80 for http, or port 443 for https</li>
</ul>

After twitter recieve's the packet, it then responds by returning data packet's of it's own. The returning packets have the destination ip address (in the ip header) set to the packet as the router's public ip address. The destination port number (in the tcp header) has the retains the same non-standard port number. This non-standard helps the router to forward the data packets to the correct internal device. But just before the router forwards the data packet to the internal device, it first replaces the source ip address to what it was originally, i.e. the internal ip address.  

 
The PAT system has limitations. Namely a if a device from the internet (aka eternal device) wants to initiate a connection to a device in an internal network, then it can't do this because the data packets sent to the router won't have a non-standard port number to help the router to do the mapping and forward on the data packets to the correct internal packet. The only way around this is to access the router's admin console, and set up <a href="https://en.wikipedia.org/wiki/Port_forwarding#Purpose" rel="nofollow">port forwarding</a>. Port forwarding is usually used for home use, but not for business. That's because it's not scalable. For example if your internal network has 2 devices that are https enable webservers, then port forwarding will only let you forward port 443 to one of these servers at a time.  



<h4>Static NAT</h4>
This is a situation where you pay extra to your ISP (Internet Service Provider) to provide additional IP addresses which are directed to your router. You then access your routers admin console and map each of your public IP addresses to each of your devices that are connected to the internal network. This effectively eliminates the need for the router to manipulate port numbers. 

In this setup it means that you can directly initiate contact with any of your devices from the outside internet. Also the internal devices can communicate with the internet without the router doing any kind of port manipulation. If you want all your internal devices to have to be accessible from the internet (using the Static NAT approach), then you need to pay your ISP for enough public IP addresses so that there's one public IP address for each internal device. 

Now let's say you have one device that's actually being used as an Apache web server. In that case you can have this web server hosting multiple websites (by using the apache vhost system). You then update public dns servers with the public IP address that the router configuration that has mapped to that device. 

Note: A data packet's IP header still get's manipulated when using Static NAT. 



<h4>Dynamic NAT</h4>




Network Address Translation is a system that gives internet access to machines that are attached to a private network. 


Here is a really good guide:

https://app.pluralsight.com/player?course=firewalls-introduction&author=ross-bagurdes&name=firewalls-introduction-m2&clip=0&mode=live


PAT:

https://www.google.co.uk/search?q=port+address+translation&ie=utf-8&oe=utf-8&client=firefox-b-ab&gfe_rd=cr&ei=B8h3WdO-NIbHXperkYAG