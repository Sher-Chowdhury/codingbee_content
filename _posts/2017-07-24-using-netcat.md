---
ID: 2110
post_title: Using netcat
author: sher
post_excerpt: >
  This is a quick guide to using the
  netcat command.
layout: post
permalink: >
  https://codingbee.net/tutorials/linux/networking/using-netcat
published: true
post_date: 2017-07-24 18:57:42
---
This is a quick guide to using the netcat command. 

Netcat is a really handy tool to check whether a certain port is open on a box, here's an example on how to do this. For example here's how to check if port 8140 is open on the box, example.com:

<pre>
$ nc -v example.com 8140
Ncat: Version 6.40 ( http://nmap.org/ncat )
Ncat: Connected to x.x.x.x:8140.
</pre>


Here's another example, but this time to check whether a port is open on the localhost:

<pre>
$ nc -v 127.0.0.1 22
Ncat: Version 6.40 ( http://nmap.org/ncat )
Ncat: Connected to 127.0.0.1:22.
SSH-2.0-OpenSSH_6.6.1
</pre>


Note: nc can't be used to check the connection of UDP ports, for example the ntp port is a udp port, with port number 123. If you try you'll get a 'no route to host' error message.