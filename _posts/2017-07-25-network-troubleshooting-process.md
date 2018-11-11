---
ID: 2154
post_title: Network troubleshooting process
author: sher
post_excerpt: >
  This article outlines a standard set of
  steps to take to try to identify which
  part of the whole setup is causing the
  problem.
layout: post
permalink: >
  https://codingbee.net/tutorials/linux/networking/network-troubleshooting-process
published: true
post_date: 2017-07-25 19:34:25
---
What's the first step you take when you encounter problem such as 'firefox isn't working'?

This article outlines a standard set of steps to take to try to identify which part of the whole setup is causing the problem.

At it's heart, the troubleshooting process we take is in line with the OSI model. In that respect our approach involves isolating in which network layer the problem resides in. The OSI network layer is
<ol>
 	<li>Physical Layer - e.g. cable not plugged in properly, or network adapters have developed a physical fault</li>
 	<li>Data Link Layer - e.g. does 'ip addr show' shows ip addresss for each network. also try using ping to ping other devices in the same box, e.g. the default gateway. then trying pinging outside the network. Also try 'arp -a'</li>
 	<li>Network Layer - e.g. in aws, this coudld be security group issues, network acls, routing...etc. also could be firewalld, iptables,....etc</li>
 	<li>Transport Layer - e.g. is the correct ports are being used. e.g. if you are trying to access a service running on port 8140,
 then try using nc or telnet to test it.</li>
 	<li>Session Layer - In this situation, it's not really a network problem, it is a application problem. E.g use netcat to simulate a listening service.</li>
 	<li>Presentation Layerr - In this situation, it's not really a network problem, it is a application problem. E.g use netcat to simulate a listening service.</li>
 	<li>Application Layerr - In this situation, it's not really a network problem, it is a application problem. E.g use netcat to simulate a listening service.</li>
</ol>