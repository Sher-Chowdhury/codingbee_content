---
ID: 476
post_title: The ss utility
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-the-ss-utility
published: true
post_date: 2015-11-01 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is the command to view all running and established connections?"]
$ ss -a
[/toggle]
[toggle title="What is the command to view all running and established tcp connections?"]
$ ss -at
[/toggle]
[toggle title="What is the command to view all running and established tcp connections, and displays port numbers in instead of service names?"]
ss -atn
[/toggle]
[toggle title="What does the output's right column show?"]
The right column actually shows internal mapping ports. 
[/toggle]
[/accordion]
<hr/>



The ss utility is the successor to the netstat utility. It is a great tool for troubleshooting networking related issues. 


Here's how to list all listening and established connections:

<pre>
$ ss -a
</pre>

As above, but filtered to only display tcp sockets:

<pre>
$ ss -at
</pre>
Note: you can use "u"

The above shows the service name (e.g. "ssh") instead of the port number (e.g. 22). If you want to display port number, then do:


<pre>
ss -atn
</pre>

Note it is displayed in the middle column. The right column actually shows internal mapping ports. 

Here's the help summary:



<pre>
$ ss --help
Usage: ss [ OPTIONS ]
       ss [ OPTIONS ] [ FILTER ]
   -h, --help           this message
   -V, --version        output version information
   -n, --numeric        don't resolve service names
   -r, --resolve       resolve host names
   -a, --all            display all sockets
   -l, --listening      display listening sockets
   -o, --options       show timer information
   -e, --extended      show detailed socket information
   -m, --memory        show socket memory usage
   -p, --processes      show process using socket
   -i, --info           show internal TCP information
   -s, --summary        show socket usage summary
   -b, --bpf           show bpf filter socket information

   -4, --ipv4          display only IP version 4 sockets
   -6, --ipv6          display only IP version 6 sockets
   -0, --packet display PACKET sockets
   -t, --tcp            display only TCP sockets
   -u, --udp            display only UDP sockets
   -d, --dccp           display only DCCP sockets
   -w, --raw            display only RAW sockets
   -x, --unix           display only Unix domain sockets
   -f, --family=FAMILY display sockets of type FAMILY

   -A, --query=QUERY, --socket=QUERY
       QUERY := {all|inet|tcp|udp|raw|unix|packet|netlink}[,QUERY]

   -D, --diag=FILE     Dump raw information about TCP sockets to FILE
   -F, --filter=FILE   read filter information from FILE
       FILTER := [ state TCP-STATE ] [ EXPRESSION ]
</pre>