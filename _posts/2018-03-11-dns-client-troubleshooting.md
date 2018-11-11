---
ID: 3006
post_title: >
  DNS – Connectiong a CentOS/RHEL 7
  client to an internal DNS server
author: sher
post_excerpt: >
  If you want to override the dhcp
  provided dns server, to a custom dns
  server, then you need to make the
  configuration via NetworkManager.
layout: post
permalink: >
  https://codingbee.net/tutorials/rhce/dns-client-troubleshooting
published: true
post_date: 2018-03-11 18:47:07
---
If you want to override the dhcp provided dns server, to a custom internal dns server, then you need to make the configuration via NetworkManager. 


You can follow along with this article using our <a href="https://github.com/Sher-Chowdhury/CentOS7-DNS-Server-demo">dns demo vagrant environment</a>.  

Let's say your custom dns server's ip address is 192.170.10.100, then before you do any configurations to start using it, first you should test to see that you can connect to it:


<pre>
[root@dns-client ~]# nc -v 192.170.10.100 53
Ncat: Version 6.40 ( http://nmap.org/ncat )
Ncat: Connected to 192.170.10.100:53.
</pre>

If that works, then the next thing to do is manually test the dns server by seding a test query:

<pre>
[root@dns-client ~]# dig  @192.170.10.100 codingbee.net

; <<>> DiG 9.9.4-RedHat-9.9.4-51.el7_4.2 <<>> @192.170.10.100 codingbee.net
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 59367
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 2, ADDITIONAL: 3

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;codingbee.net.			IN	A

;; ANSWER SECTION:
codingbee.net.		9510	IN	A	77.104.171.177

;; AUTHORITY SECTION:
codingbee.net.		81510	IN	NS	ns2.ukm11.siteground.biz.
codingbee.net.		81510	IN	NS	ns1.ukm11.siteground.biz.

;; ADDITIONAL SECTION:
ns2.ukm11.siteground.biz. 9510	IN	A	77.104.171.111
ns1.ukm11.siteground.biz. 9510	IN	A	77.104.132.42

;; Query time: 0 msec
;; SERVER: 192.170.10.100#53(192.170.10.100)
;; WHEN: Sat Apr 07 13:09:17 UTC 2018
;; MSG SIZE  rcvd: 146
</pre>

To configure your client to point to the new dns server, you need to edit <code>/etc/resolv.conf</code>. This file content in turn is managed via NetworkManager. Any existing dns IP addresses comes from the DHCP protocol. Therefore to edit this files content, we have to do it using NetworkManager. To override dchp's provided dns addresses, you can do it either via the NetworkManager's gnome gui:


<pre>$ nm-connection-editor</pre>


or via the nmcli interactive terminal. We'll demo the nmcli approach. First we get a list of active connections:

<pre>
$ nmcli connection show
</pre>

First we disable dhcp setting the dns address:

<pre>
$ nmcli connection modify System\ enp0s8 ipv4.ignore-auto-dns yes
</pre>

Then for the chosen connection run the command (use tab+tab to help create this command):

<pre>
$ nmcli connection modify System\ enp0s8 ipv4.dns '192.170.10.100'
</pre>

Behind the scenes this will end up changing adding a 'DNS1' setting to the corresponding <code>/etc/sysconfig/network-scripts/ifcfg-*</code> file. 


[post-content post_name=rhsca-quiz] 

[accordion]
[toggle title="Which file stores the ip address of the dns server?"]
/etc/resolv.conf
# the content of this is managed via NetworkManager
[/toggle]
[toggle title="What is the command to open the NetworkManager's gnome interface?"]
$ nm-connection-editor
[/toggle]
[toggle title="What are the nmcli command approach to do this?"]
$ nmcli connection show
$ nmcli connection modify {connection-name} ipv4.ignore-auto-dns yes
$ nmcli connection modify {connection-name} ipv4.dns '192.170.10.100'
[/toggle]
[toggle title="question?"]
answer
[/toggle]
[toggle title="question?"]
answer
[/toggle]
[toggle title="question?"]
answer
[/toggle]
[toggle title="question?"]
answer
[/toggle]
[/accordion]