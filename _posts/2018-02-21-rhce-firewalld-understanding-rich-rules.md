---
ID: 2548
post_title: 'Firewalld &#8211; Understanding Rich Rules on CentOS/RHEL 7'
author: sher
post_excerpt: >
  Here is a quick intro to how to use
  Firewalld rich rules.
layout: post
permalink: >
  https://codingbee.net/tutorials/rhce/rhce-firewalld-understanding-rich-rules
published: true
post_date: 2018-02-21 19:09:50
---
Rich rules are an additional feature of firewalld that allows you create more sophisticated firewall rules. For example:

<ul>
	<li>whitelist an ip range with the exception of one ip that's in this range.</li>
	<li>reroute incoming request from one port, and forward it to another port. For example, you might want people to ssh into a box using port 4234 instead of the standard port 22, for improved security. then get firewalld to reroute this traffic to sshd daemon which is listening on port 22. In this scenario, there would be no actual services listening on port 4234, but firewalld will intercept them and forward it onto port 22.</li>
	<li>limit how many incoming requests comes in per second/minute/hour/day.</li>
	<li>write particular log entries into /var/log/messages when certain requests come through</li>
</ul>


You can follow along with this article by using our <a href="https://github.com/Sher-Chowdhury/CentOS7-firewalld-rich-rules-demo">vagrant environment on github</a>. this environment is made up of 2 CentOS boxes, 'client' and 'webserver'. 

<pre>
+-------------------+            +-----------------+
|                   |            |                 |
|     Client        |            |    Webserver    |
|   (10.0.50.10)    |  Port 80   |  (10.0.50.11)   |
|                   +----------->|                 |
|                   |            |                 |
|                   |            |                 |
|                   |            |                 |
|                   |            |                 |
|                   |            |                 |
+-------------------+            +-----------------+
</pre>



The syntax for creating rich rules is quite complicated, but luckily there's a man page to help you in case you forget:

<pre>
$ man firewalld.richlanguage
</pre>

Furthermore, it's actually a lot easier to create rich rules using the firewall gui tool:

<pre>
$ firewall-config
</pre>


Here's an example of adding a rich rule via the command line:


<pre>

$ firewall-cmd --permanent --zone=public --add-rich-rule='rule family=ipv4 source address=10.0.50.10/32 service name=http log level=notice prefix="firewalld rich rule INFO:   " limit value="100/h" accept' 
success
</pre>

Here we are creating a rich rule that needs to be applied to ipv4. This rule applies when the source address is 10.0.50.10 and is seeking to access the http service. When these criteria are met notice-level log entry are made to /var/log/messages, and also this rule will limit access to 100 request per hour from this source IP. This in turn looks like this:

<pre>
[root@client ~]# firewall-cmd --zone=public --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8
  sources:
  services: ssh dhcpv6-client
  ports:
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
	rule family="ipv4" source address="10.0.50.10" service name="http" log prefix="firewalld rich rule INFO:   " level="notice" limit value="100/h" accept

</pre>



<h2>Port forwarding using rich rules</h2>


Now let's say we want to improve security of webserver by setting it up so that client can only ssh to webserver using port '42343'. At the moment this won't work:

<pre>
$ [root@client ~]# ssh -p 42343 vagrant@webserver
ssh: connect to host 10.0.50.10 port 42343: No route to host
</pre>

That's because by default the sshd daemon on webserver listens on port 22 (if we omitted the '-p 42343' bit then it would have worked). However with firewalld we can fix this issue, by configuring firewalld to:

<ul>
	<li>forward traffic from port 42343 to port 22</li>
	<li>block all traffic directly going to port 22</li>
</ul>





First we ensure firewalld daemon is running: 

<pre>
[root@webserver ~]# firewall-cmd --state        
running
</pre> 

Next we check which zones are currently active (i.e. have an interfaces/sources assigned to them):

<pre>
[root@webserver ~]# firewall-cmd --get-active-zones
public
  interfaces: enp0s3 enp0s8 enp0s9
</pre>

Only the 'public' zone is active, so let's get more details about this zone:

<pre>
[root@webserver ~]# firewall-cmd --zone=public --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8 enp0s9
  sources:
  services: ssh dhcpv6-client
  ports:
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
</pre>

As you can, this zone whitelisted ssh traffic, as long as it's for port 22. Now we want firewalld to forward traffic from port 42343 to 22, which we can set like this:


<pre>
$ firewall-cmd --zone=public --add-rich-rule='rule family=ipv4 source address=10.0.50.10 forward-port port=42343 protocol=tcp to-port=22'
</pre>


This in turn results in the rich rule section becoming populated:


<pre>
[root@webserver ~]# firewall-cmd --zone=public --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8 enp0s9
  sources:
  services: ssh dhcpv6-client
  ports:
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
	rule family="ipv4" source address="10.0.50.10" forward-port port="42343" protocol="tcp" to-port="22"
</pre>

Now if we log into the client, if we try logging in again:

<pre>
[root@client ~]# ssh -p 42343 vagrant@10.0.50.10
vagrant@10.0.50.10's password:     (in our case, the password is 'vagrant')
Last login: Thu Feb 22 12:52:37 2018 from 10.0.50.10
[vagrant@webserver ~]$
</pre>

Success! It worked. However at this stage client can still ssh in using the default port 22. So to block that off we do:

<pre>
[root@webserver ~]# firewall-cmd --remove-service=ssh --zone=public
success
</pre>

So the end result looks like this:

<pre>
[root@webserver ~]# firewall-cmd --zone=public --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8 enp0s9
  sources:
  services: dhcpv6-client
  ports:
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
	rule family="ipv4" source address="10.0.50.10" forward-port port="42343" protocol="tcp" to-port="22"
</pre>

This now has the desired result:

<pre>
[root@client ~]# ssh vagrant@10.0.50.10
ssh: connect to host 10.0.50.10 port 22: No route to host

[root@client ~]# ssh -p 42343 vagrant@10.0.50.11
vagrant@10.0.50.10's password:
Last login: Thu Feb 22 12:55:11 2018 from 10.0.50.10
[vagrant@webserver ~]$
</pre>

Note that ssh daemon on webserver is still listening on port 22, and there is no daemon actually listening on port 42343. However firewalld is monitoring the destination ports of all traffic and reroutes all port 42343 destined data packets and re-routes (aka port-forwards) them. 


Once you are happy with the firewall setting, then make them persistant by running:


<pre>
[root@webserver ~]# firewall-cmd --runtime-to-permanent
success
</pre>


<h4>Real world scenario</h4>

In the above example, we restricted ssh access to webserver to only client (10.0.50.10). However in the real world it's more likely that you want to whitelist an ip range:

<pre>
[root@webserver ~]# firewall-cmd --zone=public --add-rich-rule='rule family=ipv4 source address=10.0.50.10/24 forward-port port=42343 protocol=tcp to-port=22'
success
</pre>

...or just do port forwarding without any ip restrictions:

<pre>
[root@webserver ~]# firewall-cmd --zone=public --add-rich-rule='rule family=ipv4 forward-port port=42343 protocol=tcp to-port=22'
success
</pre>   

Another option is to not use firewalld at all, instead just reconfigure the service in question to listen on a different port number. 


<h4>Recommended Videos</h4>

https://app.pluralsight.com/library/courses/security-network-host-lfce/table-of-contents


[post-content post_name=rhsca-quiz] 

[accordion]
[toggle title="Give four possible use cases for using rich rules?"]<ul>
	<li>whitelist an ip range with the exception of one ip that's in that range.</li>
	<li>reroute incoming request from one port, and forward it to another port.</li>
	<li>limit how many incoming requests comes in per second/minute/hour/day.</li>
	<li>write particular log entries into /var/log/messages when certain requests come through</li>
</ul>
[/toggle]
[toggle title="Where can you find more info about rich rules?"]
$ man firewalld.richlanguage
[/toggle]
[toggle title="What's the easiest way to create rich rules?"]
using the GNOME desktop gui
[/toggle]
[toggle title="What is the command to start the gui?"]
$ firewall-config
[/toggle]
[toggle title="What are the 5 mains parts of a rich rule?"]
- rule (accepts 'family' setting)
- source (accepts 'address' setting)
- service (accepts 'name' setting)
- log (accepts 'level' and 'prefix' settings)
- limit (accepts 'value' setting)
[/toggle]
[toggle title="What is the general syntax to adding rich-rules?"]
$ firewall-cmd --permenent --add-rich-rule='{insert-rich-rule-here}'
[/toggle]
[toggle title="What is the command to allow and register access attempt to /var/log/messages (you can use man pages to work this out), with priority info, and log these entries starting with 'firewalld rich rule INFO:   '. where source is from ip address 10.0.50.10, access is for http service. Also limit access to 100 access per hour."]
$ firewall-cmd --permanent --zone=public --add-rich-rule='rule family=ipv4 source address=10.0.50.10/32 service name=http log level=notice prefix="firewalld rich rule INFO:   " limit value="100/h" accept'
success
$ systemctl restart firewalld
# note, this will give the source access, even if http isn't added as a service itself. 
[/toggle]
[toggle title="How do you set up port forwarding from port 12345 to port 22 for the ip range 10.0.50.10/24,?"]
$ firewall-cmd --add-rich-rule='rule family=ipv4 source address=10.0.50.10/24 forward-port port=12345 protocol=tcp to-port=22'
[/toggle]
[toggle title="What is the command to make non-persistant configuratins permanent?"]
$ firewall-cmd --runtime-to-permanent
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