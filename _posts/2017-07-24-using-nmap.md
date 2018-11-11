---
ID: 2119
post_title: Using nmap
author: sher
post_excerpt: |
  nmap (aka network mapper) is used to identify what ports are open for a given vm.
layout: post
permalink: >
  https://codingbee.net/tutorials/linux/networking/using-nmap
published: true
post_date: 2017-07-24 19:04:00
---
nmap (aka network mapper) is used to identify what ports are open for a given vm. 

<pre>
$ yum install nmap
</pre>


then simply run:

<pre>$ nmap {ip-address}</pre>

or 

<pre>$ nmap example.com</pre>

You can also scan a range of ip addresses:

<pre>$ nmap 192.168.1.1-100</pre>


nmap is useful if you want to quickly list out all open ports for localhost:


<pre>$ nmap localhost</pre>