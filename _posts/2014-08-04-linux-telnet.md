---
ID: 133
post_title: 'linux &#8211; telnet'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/uncategorized/linux-telnet
published: true
post_date: 2014-08-04 00:00:00
---
telnet comes in 2 parts,

&nbsp;

- telnet (client)

- telnet-server (daemon, that will by default listen on port 23)

&nbsp;

http://www.unixmen.com/how-to-install-telnet-in-centos-rhel-scientific-linux-6-4/

&nbsp;

Telnet is also really useful to check if a certain port on a remote machine is open, here's the format:

<pre>
$ telnet {ip number} {port number}
</pre>

For example, to check port 22, we do:

<pre>
telnet 192.168.52.201 22
Trying 192.168.52.201...
Connected to home-directories.
Escape character is '^]'.
SSH-2.0-OpenSSH_6.6.1
</pre>

This shows port 22 is open, whereas if we check a port that is closed, e.g. in my case 21, then we get:

<pre>
[root@openldap-client01 ~]# telnet 192.168.52.201 21
Trying 192.168.52.201...
telnet: connect to address 192.168.52.201: Connection refused
</pre>



&nbsp;

also install nmap:

&nbsp;

yum install nmap

&nbsp;

after that you can check if telnet daemon is listenning on the correct port:

[bash][root@puppetagent1 ~]# nmap localhost

Starting Nmap 5.51 ( http://nmap.org ) at 2014-08-04 02:08 BST
Nmap scan report for localhost (127.0.0.1)
Host is up (0.000019s latency).
Other addresses for localhost (not scanned): 127.0.0.1
Not shown: 995 closed ports
PORT       STATE SERVICE
22/tcp   open   ssh
23/tcp   open   telnet
25/tcp   open   smtp
111/tcp open   rpcbind
631/tcp open   ipp [/bash]

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;