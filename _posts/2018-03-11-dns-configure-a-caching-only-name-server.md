---
ID: 2980
post_title: >
  DNS – Configure a caching-only name
  server on CentOS/RHEL 7
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhce/dns-configure-a-caching-only-name-server
published: true
post_date: 2018-03-11 13:13:31
---
A caching-only DNS server is a server that sits inside an internal network that all the other boxes in the internal network uses when it wants they do a dns lookup. You can follow along with this article using our <a href="https://github.com/Sher-Chowdhury/CentOS7-DNS-Server-demo">dns demo vagrant environment</a>.  
 
A caching-only DNS server doesn't have a full dns db, instead it would query actual dns servers to get the info and then feed it back to the requester. It will than cache the request so that it responds quicker if the request is made again.	 	
There's a few reason for using caching-only DNS servers:	 	 
<ul>	 	 
 <li><strong>Better security</strong> - internal servers can do dns lookup within the internal network, so can close the dns port from network to the public</li>	 	 
 <li><strong>Better performance</strong> - you get faster response times if dns lookup a server want is already cached</li>	 	 
</ul>	 	 
To set up a cache-only dns server, you need to first install the main dns software:	 	 
<pre>
$ yum install bind
</pre>
This software let's you build all four types of DNS servers:	 	 
<ul>	 	 
 <li>Master DNS Server</li>	 	 
 <li>Slave DNS server</li>	 	 
 <li>Caching-only DNS server</li>	 	 
 <li>Forwarding-only DNS server</li>	 	 
</ul>	 	 
The main (and only) config file that needs to be edited to set up a caching-only server is <code>/etc/named.conf</code>. By default this file is already preconfigured for a Caching-Only Server but only few extra tweaks are required:	 	 


<pre>[root@webserver ~]# cat /etc/named.conf
//
// named.conf
//
// Provided by Red Hat bind package to configure the ISC BIND named(8) DNS
// server as a caching only nameserver (as a localhost DNS resolver only).
//
// See /usr/share/doc/bind*/sample/ for example named configuration files.
//
// See the BIND Administrator's Reference Manual (ARM) for details about the
// configuration located in /usr/share/doc/bind-{version}/Bv9ARM.html

options {
        <strong>// change this to 'any'</strong>
	listen-on port 53 { <strong>127.0.0.1</strong>; };
	listen-on-v6 port 53 { ::1; };
	directory 	"/var/named";
	dump-file 	"/var/named/data/cache_dump.db";
	statistics-file "/var/named/data/named_stats.txt";
	memstatistics-file "/var/named/data/named_mem_stats.txt";
        <strong>// change this to 'any'</strong>
	allow-query     { <strong>localhost</strong>; };

	/*
	 - If you are building an AUTHORITATIVE DNS server, do NOT enable recursion.
	 - If you are building a RECURSIVE (caching) DNS server, you need to enable
	   recursion.
	 - If your recursive DNS server has a public IP address, you MUST enable access
	   control to limit queries to your legitimate users. Failing to do so will
	   cause your server to become part of large scale DNS amplification
	   attacks. Implementing BCP38 within your network would greatly
	   reduce such attack surface
	*/
	recursion yes;

	dnssec-enable yes;
        <strong>// change this to 'no'</strong>
	dnssec-validation <strong>yes</strong>;

	/* Path to ISC DLV key */
	bindkeys-file "/etc/named.iscdlv.key";

	managed-keys-directory "/var/named/dynamic";

	pid-file "/run/named/named.pid";
	session-keyfile "/run/named/session.key";
};

logging {
        channel default_debug {
                file "data/named.run";
                severity dynamic;
        };
};

zone "." IN {
	type hint;
	file "named.ca";
};

include "/etc/named.rfc1912.zones";
include "/etc/named.root.key"; 
</pre>

Bind exclusively uses IPV6 which isn't fully supported yet. So for maximum compatibility you need to append the  append the following line to <code>/etc/sysconfig/named</code> to enable IPV4:

<pre>OPTIONS="-4"</pre>

For more info, see <code>man named</code>. Next you can run the following command to check if you made any syntax errors:


<pre>
$ named-checkconf
$
</pre>

This will give if all is ok. 

Next we all dns in firewalld:


<pre>
$ firewall-cmd --permanent --add-service=dns
$ systemctl restart firewalld
</pre>

This effectively opens up the dns port, which is port 53. Finally we start and enable the dns service:


<pre>
$ systemctl enable named
Created symlink from /etc/systemd/system/multi-user.target.wants/named.service to /usr/lib/systemd/system/named.service.
$ systemctl start named
</pre>

Next we need to test if this has worked. You can use nslookup or the dig command to test this. First let's do test the lookups by using an ordinary public server:


<pre>
[root@webserver ~]# nslookup codingbee.net
Server:		10.0.2.3
Address:	10.0.2.3#53

Non-authoritative answer:
Name:	codingbee.net
Address: 77.104.171.177



[root@webserver ~]# dig codingbee.net

; <<>> DiG 9.9.4-RedHat-9.9.4-51.el7_4.2 <<>> codingbee.net
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 62918
;; flags: qr rd ra ad; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;codingbee.net.			IN	A

;; ANSWER SECTION:
codingbee.net.		11522	IN	A	77.104.171.177

;; Query time: 2 msec
;; SERVER: 10.0.2.3#53(10.0.2.3)
;; WHEN: Sun Mar 11 17:33:08 UTC 2018
;; MSG SIZE  rcvd: 47
</pre>


Now we do this again, but this time explicit we send the resolution request to our new caching-only server:


<pre>
[root@webserver ~]# nslookup codingbee.net 127.0.0.1
Server:		127.0.0.1
Address:	127.0.0.1#53

Non-authoritative answer:
Name:	codingbee.net
Address: 77.104.171.177

[root@webserver ~]# dig codingbee.net @127.0.0.1

; <<>> DiG 9.9.4-RedHat-9.9.4-51.el7_4.2 <<>> codingbee.net @127.0.0.1
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 249
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 2, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;codingbee.net.			IN	A

;; ANSWER SECTION:
codingbee.net.		13933	IN	A	77.104.171.177

;; AUTHORITY SECTION:
codingbee.net.		172332	IN	NS	ns2.ukm11.siteground.biz.
codingbee.net.		172332	IN	NS	ns1.ukm11.siteground.biz.

;; Query time: 0 msec
;; SERVER: 127.0.0.1#53(127.0.0.1)
;; WHEN: Sun Mar 11 17:46:34 UTC 2018
;; MSG SIZE  rcvd: 114
</pre>


Now to let other boxes in the private network to start using this caching-only dns server, you just have to update the other boxes 'nameserver' setting in the <code>/etc/resolv.conf</code> file. 


[post-content post_name=rhsca-quiz] 

[accordion]
[toggle title="What is the command to install the dns software?"]
$ yum install bind
[/toggle]
[toggle title="What is the main config file?"]
/etc/named.conf
[/toggle]
[toggle title="What changes need to be made to this file?"]
- Set the 'listen-on port 53' to 'any'
- set the allow-query to 'any'
- set dnssec-validation to 'no'
[/toggle]
[toggle title="What other file needs changing?"]
/etc/sysconfig/named
You need to append:
OPTIONS="-4"
[/toggle]
[toggle title="What is the command to syntax check our config files?"]
$ named-checkconf
[/toggle]
[toggle title="What is the commands to open up dns firewalls?"]
$ firewall-cmd --permanent --add-service=dns
$ systemctl restart dns
[/toggle]
[toggle title="What is the command to start and enable the dns service?"]
$ systemctl start named
$ systemctl enable named
[/toggle]
[toggle title="What is a command to check if your caching-only dns server is working?"]
$ nslookup codingbee.net 127.0.0.1
# or:
$ dig codingbee.net @127.0.0.1
[/toggle]
[/accordion]