---
ID: 2084
post_title: Public and Private IP Addresses
author: sher
post_excerpt: >
  A public IP address allows a machine to
  be accessible from the internet, whereas
  a private IP address allows machine to
  be accessible by other machines that are
  also attached to the same network.
layout: post
permalink: >
  https://codingbee.net/tutorials/linux/networking/public-and-private-ip-addresses
published: true
post_date: 2017-07-23 23:00:48
---
A linux machine can have multiple network cards installed (some of them can be virtual devices). Each Adapter can have either a public or private address attached to them. 

It has been internationally agreed by all network hardware manufacturers that the following ip addresses ranges are reserved for use inside private networks (aka local networks, aka Local Area Networks aka LANs):

- 10.0.0.0/8  (This lets you build a private network of 255x255x255 hosts)
- 172.16.0.0/12 
- 192.168.0.0/16

These addresses are referred to as <a href="https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces" rel="nofollow">Private IPv4 addresses namespaces</a>.

This means that there are no device on the internet that has an ip address that falls in the range of the above three. This also means that these ranges of ip address can be re-used over and over again inside LANs, where each of these IP addresses are unique within the scope of the LAN that the device resides in.  

The magic that lets a device in one LAN to communicate over the internet to a device that belongs to another LAN, is done through a technology called <a href="http://codingbee.net/tutorials/linux/networking/network-address-translation-nat">Network Address Translation (NAT)</a>.



https://en.wikipedia.org/wiki/Reserved_IP_addresses

if an interfaces has an IP address that belongs within any of these ranges, then that interface is attached to a private network. 


https://en.wikipedia.org/wiki/Reserved_IP_addresses

https://app.pluralsight.com/player?course=linux-networking-advanced-lfce&author=anthony-nocentino&name=linux-networking-advanced-lfce-m3&clip=5&mode=live