---
ID: 2342
post_title: >
  Real world examples of using firewalld
  on CentOS/RHEL 7
author: sher
post_excerpt: "We are now going to walk through a number of firewalld setups that's often seen in the real world. "
layout: post
permalink: >
  https://codingbee.net/tutorials/rhce/rhce-real-world-examples-of-using-firewalld
published: true
post_date: 2017-08-23 21:27:37
---
Previously we gave a brief <a href="http://codingbee.net/tutorials/rhcsa/rhcsa-using-firewalld">introduction to firewalld</a>, but we are now going to walk through a few firewalld setups that's often seen in the real world. 

I have created a vagrant project that you can use to follow along in this tutorial. We will use the following demo setup throughout this article:

<pre>
+---------------------------------------------------+
|                webserver.local                    |
|          +------------------------+               |
|          |  Apache (httpd daemon) |               |
|          |  Listening on port 80  |               |
|          +------------------------+               |
|                      ^                            |
|                      |                            |
|                      v                            |
|      +-----------------------------------+        |
|      |            Firewalld              |        |
|      +-------+----------------------+----+        |
|              ^                      ^             |
|              |                      |             |
|              v                      v             |
|  +-------------------+    +-------------------+   |
|  |                   |    |                   |   |
|  |     enp0s9        |    |     enp0s8        |   |
|  |   10.0.0.10/24    |    | 192.168.50.10/24  |   |
|  |                   |    |                   |   |
|  +-------------------+    +-------------------+   |
|        ^     ^                             ^      |
|        |     |                             |      |
+---------------------------------------------------+
         |     |                             |
         |     +------------+                |
         v                  v                v
 +----------------+   +---------------+   +------------+
 |   box1.local   |   |  box2.local   |   |Internet    |
 |  10.0.0.11/24  |   | 10.0.0.12/24  |   |(my laptop) |
 +----------------+   +---------------+   +------------+
</pre>

If you use spin up the above vagrant project, you will end up with three CentOS7 VMs:

<ul>
	<li>webserver.local</li>
	<li>box1.local</li>
	<li>box2.local</li>
</ul>

All three boxes belongs to the same private network, 10.0.0.0/24. However webserver.local has an extra interface enp0s8 that has been setup to receive access from the internet. To keep things simple, I've configured my laptop (which is hosting the VMs) to act as as a device from the public internet. 


Now before we can start demoing firewalld, we first need to install httpd on webserver.local, and run it:


<pre>
$ yum install -y httpd

$ echo 'hello-world' > /var/www/html/index.html

$ chown apache:apache /var/www/html/index.html

$ systemctl start httpd

$ systemctl enable httpd
</pre>

Now let's test this:

<pre>
[root@webserver ~]# curl http://localhost
hello-world
</pre>


Now we are ready to start going through some real life scenarios:

<ul>
	<li>Example 1: Give everything access to the http service</li>
	<li>Example 2: Deny access to everything</li>
	<li>Example 3: Only give access to httpd for boxes in the same private network, and deny access to everything else</li>
	<li>Example 4: Give only box1 access, and deny access to everything else</li>
</ul>



<h3>Example 1: Give everything access to httpd service</h3>
Let's first confirm that our apache service is running and is listening on port 80:

<pre>
[root@webserver ~]# systemctl status httpd
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; vendor preset: disabled)
   Active: active (running) since Thu 2017-08-31 13:48:24 UTC; 21min ago
     Docs: man:httpd(8)
           man:apachectl(8)
 Main PID: 1045 (httpd)
   Status: "Total requests: 4; Current requests/sec: 0; Current traffic:   0 B/sec"
   CGroup: /system.slice/httpd.service
           ├─1045 /usr/sbin/httpd -DFOREGROUND
           ├─1174 /usr/sbin/httpd -DFOREGROUND
           ├─1175 /usr/sbin/httpd -DFOREGROUND
           ├─1176 /usr/sbin/httpd -DFOREGROUND
           ├─1179 /usr/sbin/httpd -DFOREGROUND
           └─1180 /usr/sbin/httpd -DFOREGROUND

Aug 31 13:48:23 webserver.local systemd[1]: Starting The Apache HTTP Server...
Aug 31 13:48:24 webserver.local systemd[1]: Started The Apache HTTP Server.
[root@webserver ~]# netstat -tulpen | grep 1045
tcp6       0      0 :::80                   :::*                    LISTEN      0          17859      1045/httpd
</pre>


At the moment I haven't started firewalld yet:

<pre>
[root@webserver ~]# systemctl status firewalld
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; disabled; vendor preset: enabled)
   Active: inactive (dead)
     Docs: man:firewalld(1)
</pre>

That means that the webservers network interfaces can send traffic directly to Apache, without data packets being intercepted by firewalld. This means that box1, box2, and the outside internet (e.g. my macbook) can access the hello-world web page:

<pre>
[root@box1 ~]# curl http://10.0.0.10
hello-world

[root@box2 ~]# curl http://10.0.0.10
hello-world

# from my macbook
schowdhury@Shers-MacBook-Pro:~$ curl http://192.168.50.10
hello-world

# The webserver can access itself
[root@webserver ~]# curl http://127.0.0.1
hello-world
</pre>

So that means everything can access the apache service if the firewalld deamon is not running. Everything can continue to access the apache daemon while firewalld is runnng and we'll cover that as part of the next example. 



<h3>Example 2: Deny access to everything</h3>

This is done by simply starting the service:

<pre>
[root@webserver ~]# systemctl start firewalld
[root@webserver ~]# systemctl enable firewalld
Created symlink from /etc/systemd/system/dbus-org.fedoraproject.FirewallD1.service to /usr/lib/systemd/system/firewalld.service.
Created symlink from /etc/systemd/system/basic.target.wants/firewalld.service to /usr/lib/systemd/system/firewalld.service.
[root@webserver ~]# systemctl status firewalld
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; enabled; vendor preset: enabled)
   Active: active (running) since Wed 2017-08-23 21:33:12 UTC; 10s ago
     Docs: man:firewalld(1)
 Main PID: 19875 (firewalld)
   CGroup: /system.slice/firewalld.service
           └─19875 /usr/bin/python -Es /usr/sbin/firewalld --nofork --nopid

Aug 23 21:33:11 webserver.local systemd[1]: Starting firewalld - dynamic firewall daemon...
Aug 23 21:33:12 webserver.local systemd[1]: Started firewalld - dynamic firewall daemon.
</pre>

Let's now check which zone has been activated:

<pre>
[root@webserver ~]# firewall-cmd --get-active-zones
public
  interfaces: enp0s3 enp0s8 enp0s9
</pre>

Note: an active zone is a zone where either the 'interfaces' or 'sources' (or both) fields are populated. 

This shows that any incoming network traffic that is coming into a box via interfaces enp0s3, enp0s8, or enp0s9 will get intercepted by firewalld, and firewalld will then decide whether to let the traffic through. If we get more detailed information about the public zone:

<pre>
[root@webserver ~]# firewall-cmd --zone=public --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8 enp0s9
  sources:
  services: dhcpv6-client ssh
  ports:
  protocols:
  masquerade: no
  forward-ports:
  sourceports:
  icmp-blocks:
  rich rules:
</pre>


Here we see that incoming network traffic that is coming into a box via interfaces enp0s3, enp0s8, and enp0s9 is allowed as long as the network traffic is destined to for the 'dhcpv6-client' and 'ssh' service. Note, dhcpv6-client is required for dchp to work. If firewalld blocks then you would need to manually setup internet connection settings rather than letting dhcp do this for your automatically.

Notice here, that the public zone doesn't allow http traffic on any interface. That means box1, box2, and my laptop has now lost access to the hello-world webpage. That's because firewalld is blocking all traffic destined for httpd/80, for example:

<pre>
[root@box1 ~]# curl http://10.0.0.10
curl: (7) Failed connect to 10.0.0.10:80; No route to host
</pre>

Now let's configure the 'public' zone to allow all httpd/80 related traffic. First let's find the right service to whitelist:

<pre>
[root@webserver ~]# firewall-cmd --get-services | sed 's/ /\n/g'
RH-Satellite-6
amanda-client
amanda-k5-client
.
.
.
ftp
high-availability
<strong>http</strong>
https
imap
.
.
...etc
</pre>


Now lets add the http service, and load that in:

<pre>
[root@webserver ~]# firewall-cmd --zone=public --add-service=http
success
[root@webserver ~]# firewall-cmd --zone=public --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8 enp0s9
  sources:
  services: dhcpv6-client <strong>http</strong> ssh
  ports:
  protocols:
  masquerade: no
  forward-ports:
  sourceports:
  icmp-blocks:
  rich rules:

</pre>

Note: I used the 'zone' flag only to be explicit to which zone this rule should be applied to. If I didn't specify the zone flag, then the rule would get applied to which ever zone is the designated default zone. Also the default zone is just a zone where changes are made to if the --zone flag isn't used. 


Note: we could have done this by whitelisting port 80:

<pre>
[root@box1 ~]# firewall-cmd --zone=public --permanent --add-port=80/tcp
success</pre>


But that is a matter of preference. This ends up restoring access again for box1, box2, and the internet, e.g.:


<pre>
[root@box1 ~]# curl http://10.0.0.10
hello-world
</pre>

Also if you want to make this firewalld config persistant, then you need to run the earlier command again, but with the permanent setting:


<pre>
[root@box1 ~]# firewall-cmd --zone=public --permanent --add-service=http
success
</pre>

Also if you have made several non-permenant firewall-cmd related change and you are happy to make them all persistant, then a quicker alternative is to run:


<pre>
[root@box1 ~]# firewall-cmd --runtime-to-permanent
success
</pre>



<h3>Example 3: Only give access to httpd for boxes in the same private network, and deny access to everything else</h3>

Now let's say we want to only allow access from box1 and box2 access, but not the rest of the internet (e.g. my laptop). Then one way to do this (which actually doesn't work, as explained further below) is to run something like this:


<pre>
[root@webserver ~]# firewall-cmd --zone=public --add-source=10.0.0.0/24
success

[root@webserver ~]# firewall-cmd --zone=public --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8 enp0s9
  sources: 10.0.0.0/24
  services: dhcpv6-client http ssh
  ports:
  protocols:
  masquerade: no
  forward-ports:
  sourceports:
  icmp-blocks:
  rich rules:

</pre>


This on first impressions appears to suggest that only traffic from source ip addresses that are in the range 10.0.0.0/24 (i.e. box1 and box2) should be able to access the hello-world webpage. So my laptop's browser should lose access. However that's not the case. That's because what the above output is actually saying is that network traffic from any of the following sources:

<ul>
	<li>enp0s3</li>
	<li>enp0s8</li>
	<li>enp0s9</li>
	<li>10.0.0.0/24</li>
</ul>

Is allowed to access any of the following services:

<ul>
	<li>dhcpv6-client</li>
	<li>http</li>
	<li>ssh</li>
</ul>

So if you want to restrict the laptop browser's access, but still allow box1 and box2 to access the hello-world page, to achieve this we need to first undo all the firewalld config made so far, and since our changes are non-persistant, we can simply reset everything by restarting the firewalld daemon:

<pre>
[root@webserver ~]# systemctl restart firewalld
</pre>

Next, in order to achieve what we want, we need to make use of another zone (in addition to the public zone) which we will configure to all restricted access to the hello-world page:

<pre>
[root@webserver ~]# firewall-cmd --get-zones
work drop internal external trusted home dmz public block
</pre>

It doesn't matter which zone we choose, but I'll choose the 'trusted' zone.  

   
<pre>
[root@webserver ~]# firewall-cmd --zone=trusted --list-all
trusted
  target: ACCEPT
  icmp-block-inversion: no
  interfaces:
  sources:
  services:
  ports:
  protocols:
  masquerade: no
  forward-ports:
  sourceports:
  icmp-blocks:
  rich rules:
</pre>

At the moment this zone doesn't allow anything. Now let's configure this zone to whitelist permitted sources and destination:

<pre>
[root@webserver ~]# firewall-cmd --zone=trusted --add-service=http
success
</pre>

Next let's whitelist our ip address range, like we did last time: 

<pre>
[root@webserver ~]# firewall-cmd --zone=trusted --add-source=10.0.0.0/24
success

# alternatively we could have also done the following to achieve the same overall effect:
[root@webserver ~]#  firewall-cmd --zone=trusted --change-interface=enp0s9
# This also happens to be persistent because the NetworkManager daemon is controlling this interface. 
</pre>



This results in:


<pre>
[root@webserver ~]# firewall-cmd --zone=trusted --list-all
trusted (active)
  target: ACCEPT
  icmp-block-inversion: no
  interfaces:
  <strong>sources: 10.0.0.0/24</strong>
  <strong>services: http</strong>
  ports:
  protocols:
  masquerade: no
  forward-ports:
  sourceports:
  icmp-blocks:
  rich rules:
</pre>


Next we can confirm that both public and trusted zones are both enforcing restrictions:

<pre>
[root@webserver ~]# firewall-cmd --get-active-zones
public
  interfaces: enp0s3 enp0s8 enp0s9
trusted
  sources: 10.0.0.0/24
</pre>


As you can see we have 2 zones active. The public zone gives us the ssh access from any box:



<pre>
[root@webserver ~]# firewall-cmd --zone=public --list-all
public (active)
  target: default
  icmp-block-inversion: no
  <strong>interfaces: enp0s3 enp0s8 enp0s9</strong>
  sources:
  services: dhcpv6-client <strong>ssh</strong>
  ports:
  protocols:
  masquerade: no
  forward-ports:
  sourceports:
  icmp-blocks:
  rich rules:
</pre>
  
Whereas the trusted zone, controls access to the http service. At this stage, it means that box1 and box2 can now access the hello-world page:


<pre>
[root@box1 ~]# curl http://10.0.0.10
hello-world

[root@box2 ~]# curl http://10.0.0.10
hello-world
</pre>


But not from the laptop:

<pre>
schowdhury@Shers-MacBook-Pro:~$ curl http://192.168.50.10
curl: (7) Failed to connect to 192.168.50.10 port 80: Connection refused
</pre>

<h3>Example 4: Give only box1 access, and deny access to everything else</h3>


Now another scenario could be to only give box1 access to hello-world web page, to do this let first reset everything, then apply a rule at the ip level only:

<pre>
[root@webserver ~]# systemctl restart firewalld
[root@webserver ~]# firewall-cmd --zone=trusted --add-service=http
success
[root@webserver ~]# firewall-cmd --zone=trusted --add-source=10.0.0.11/32
success
</pre>


This ends up with:

<pre>
[root@box1 ~]# curl http://10.0.0.10
hello-world

[root@box2 ~]# curl http://10.0.0.10
curl: (7) Failed connect to 10.0.0.10:80; No route to host

schowdhury@Shers-MacBook-Pro:~$ curl http://192.168.50.10
curl: (7) Failed to connect to 192.168.50.10 port 80: Connection refused
</pre>




[post-content post_name=rhsca-quiz] 

This quiz relates to the diagram at the top. Also we'll assume httpd is already installed and running. 

[accordion]
[toggle title="Name the four common scenario?"]
<ul>
	<li>Example 1: Give everything access to the http service</li>
	<li>Example 2: Deny access to everything</li>
	<li>Example 3: Only give access to httpd for boxes in the same private network, and deny access to everything else</li>
	<li>Example 4: Give only box1 access, and deny access to everything else</li>
</ul>
[/toggle]
[toggle title="Is it possible for more than one firewalld zone to be active?"]
yes
[/toggle]
[toggle title="What do you need to do to activate a zone?"]
Associate a source (e.g. ip address range) or interfaces (e.g. enp0s8) to a zone
[/toggle]
[toggle title="What is the command to get summary of all active zones?"]
$ firewall-cmd --get-active-zones
[/toggle]
[toggle title="What is the command to get a more detailed view of all active zones?"]
$ firewall-cmd --list-all
[/toggle]
[toggle title="What is the command to get a list of all available services?"]
$ firewall-cmd --get-services
[/toggle]
[toggle title="What is the command to non-persistently whitelist http traffic?"]
$ firewall-cmd --zone=public --add-service=http
[/toggle]
[toggle title="How do you do that same thing but this time with the port number?"]
$ firewall-cmd --zone=public --add-port=80/tcp
[/toggle]
[toggle title="How to make the above two commands persistant?"]
$ firewall-cmd --zone=public --permanent --add-service=http
$ firewall-cmd --zone=public --permanent --add-port=80/tcp
[/toggle]
[toggle title="What is the command to persistantly whitelist traffic from the range 10.0.0.0/24?"]
$ firewall-cmd --zone=public --add-source=10.0.0.0/24
$ systemctl restart firewalld
[/toggle]
[toggle title="What is the command to get a list of all zones?"]
$ firewall-cmd --get-zones
[/toggle]
[toggle title="What is the command to whitelist the ip address 10.0.0.11?"]
$ firewall-cmd --zone=trusted --add-source=10.0.0.11/32
[/toggle]
[toggle title="What is the command to get detailed view of all zones, active or inactive?"]
$ firewall-cmd --list-all-zones
[/toggle]
[/accordion]