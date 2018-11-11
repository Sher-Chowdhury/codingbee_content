---
ID: 430
post_title: 'RHCSA &#8211; Using Firewalld'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-using-firewalld
published: true
post_date: 2015-06-21 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is the command to check the status of the firewall's service?"]
$ systemctl status firewalld
[/toggle]
[toggle title="What is the command to list all the available zones?"]
$  firewall-cmd <span style="letter-spacing:1.5px">-</span>-get-zones
[/toggle]
[toggle title="What is the command to view the default zone?"]
$ firewall-cmd <span style="letter-spacing:1.5px">-</span>-get-default-zone
[/toggle]
[toggle title="What is the command to view the currently active zone?"]
$ firewall-cmd <span style="letter-spacing:1.5px">-</span>-get-active-zones
[/toggle]
[toggle title="What is the command to view info about the zone, 'public'?"]
$ firewall-cmd <span style="letter-spacing:1.5px">-</span>-zone=public <span style="letter-spacing:1.5px">-</span>-list-all
[/toggle]
[toggle title="What is the command to add an IP address (192.168.50.0/24) restriction to the zone, 'public'. Also this needs to be a persistant restriction?"]
$ firewall-cmd <span style="letter-spacing:1.5px">-</span>-zone=public <span style="letter-spacing:1.5px">-</span>-add-source=192.168.50.0/24 <span style="letter-spacing:1.5px">-</span>-permanent
# then restart the firewalld service to load in the change
$ systemctl restart firewalld
[/toggle]
[toggle title="What is the command to view a list of services that can be added to a zone?"]
$ firewall-cmd <span style="letter-spacing:1.5px">-</span>-get-services
[/toggle]
[toggle title="What are the commands to peristantly add the http service to the 'public' zone?"]
$ firewall-cmd <span style="letter-spacing:1.5px">-</span>-add-service=http <span style="letter-spacing:1.5px">-</span>-zone=public <span style="letter-spacing:1.5px">-</span>-permanent
$ systemctl restart firewalld     # load in the new change
[/toggle]
[toggle title="What are the commands to persistantly add port 80/tcp to the zone public?"]
$ firewall-cmd <span style="letter-spacing:1.5px">-</span>-add-port=80/tcp <span style="letter-spacing:1.5px">-</span>-permanent <span style="letter-spacing:1.5px">-</span>-zone=public
$ systemctl restart firewalld
[/toggle]
[toggle title="What is the command to check that the above changes have worked?"]
$ firewall-cmd <span style="letter-spacing:1.5px">-</span>-list-all
[/toggle]
[toggle title="Which directory contains xml representations of each 'firewalld' service, which can use as templates to create your own templates?"]
/usr/lib/firewalld/services/
[/toggle]
[toggle title="If you create your custom firewalld service xml files, then which directory do you need to store them in?"]
/etc/firewalld/services
[/toggle]
[toggle title="What is the command to open up firewall gui interface?"]
$ firewall-config
[/toggle]
[/accordion]

<hr/>

A firewall is a software that controls what network traffic is allowed to pass through. CentOS 7 comes with a firewall software called <strong>firewalld</strong>, which is installed by default on a standard CentOS 7 machine. However just in case it isn't installed, then you can install it by running:

<pre>
$ yum install firewalld firewall-config
</pre>

firewall-config is optional and it is gui interface for configuring firewalld. Firewalld itself is fully configurable by it's command line utility, which is called firewall-cmd. 

Firewalld runs in the form of a service:


<pre>
$ systemctl status firewalld
firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; enabled)
   Active: active (running) since Sat 2015-06-20 07:20:30 BST; 1 day 7h ago
 Main PID: 611 (firewalld)
   CGroup: /system.slice/firewalld.service
           └─611 /usr/bin/python -Es /usr/sbin/firewalld --nofork --nopid

Jun 20 07:20:26 localhost.localdomain systemd[1]: Starting firewalld - dynamic firewall daemon...
Jun 20 07:20:30 localhost.localdomain systemd[1]: Started firewalld - dynamic firewall daemon.
</pre>

The main way to manage and configure firewalld is via the firewall-cmd command, more about firewall-cmd later on.  

Firewalld isn't just a firewall software, it also has a few network routing related features too. Here are the main firewalld features:


<ul>
	<li>Allow data packets into and out of a system.</li>
	<li>Reject data packets that fails to satisfy the allow rules</li>
	<li>Drop data packets without sending a response message to the source device</li>
	<li>Can do port forwarding - i.e. can associate a port to a specific device on the network, so that network traffic is routed to/from that device, if source device is trying to interact with the given port</li>
	<li>Can act as a router to forward data packets between 2 or more local networks (more about this in the RHCE course). This means it can perform the function of Network Address Translation (NAT). In Firewalld, NAT is referred to as masquarading</li>

</ul>


When it comes to firewalld, it's quite common to regularly change the firewalld's settings. For example let's say you have a CentOS 7 laptop with firewalld is running on it. You might want to change the firewalld settings when at your workplace, then change it again when you're using the laptop at home, then change it again when you are connecting to a public wifi, e.g. at a coffee shop. Manually changing the firewalld settings this frequently can get really tedious, especially considering that the firewalld settings are written in xml format. That's why firewalld comes equipped with the ability to have multiple versions of these xml files, and you can tell firewalld which  version of xml file to load in. Therefore firewalld essentially let's you switch between one firewalld mode to another. In firewalld, these modes are referred to as 'zones'.

Firewalld comes included with a list of generic zones. You can list these zones using firewall-cmd:

<pre>
$ firewall-cmd --get-zones | sed 's/ /\n/g'
work
drop
internal
external
trusted
home
dmz
public
block
</pre>

Note: I piped it the sed command to makes it easier to read.

The underlying xml files for each of these zones can be found here:

<pre>
$ pwd
/usr/lib/firewalld/zones
$ ll
total 36
-rw-r--r--. 1 root root 299 May 25 20:21 block.xml
-rw-r--r--. 1 root root 293 May 25 20:21 dmz.xml
-rw-r--r--. 1 root root 291 May 25 20:21 drop.xml
-rw-r--r--. 1 root root 304 May 25 20:21 external.xml
-rw-r--r--. 1 root root 369 May 25 20:21 home.xml
-rw-r--r--. 1 root root 384 May 25 20:21 internal.xml
-rw-r--r--. 1 root root 315 May 25 20:21 public.xml
-rw-r--r--. 1 root root 162 May 25 20:21 trusted.xml
-rw-r--r--. 1 root root 311 May 25 20:21 work.xml

</pre>

To find out which zone is currently active, we run:

<pre>
$ firewall-cmd --get-active-zones
public
  interfaces: enp0s3 enp0s8 enp0s9
</pre>


This shows that that the zone 'public' is active and that this firewall zone is controlling the network traffic that are passing through the enp0s3, enp0s8, and enp0s9 interfaces. The cool thing with the concept of zones is that you can have multiple active zones. for example each zone being attached to a specific interface or whitelist a particular ip range. Also to make a zone active, you simply needs to populate one of the following zone settings:

<ol>
	<li>interface (e.g. firewall-cmd --zone=trusted --add-interface=enp0s8) </li>
	<li>source   (e.g. firewall-cmd --zone=dmz --add-source=10.0.0.0/24)</li>
</ol>




    

To get the full details of  a given zone's settings, we run:


<pre>
$ firewall-cmd --zone=public --list-all
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

The --list-all option gives an overview of the entire zone, i.e. which interfaces is being controlled by this zone, what services are allowed to send+receive data in this zone. 

Note that the public zone shown above is also the default zone, you can see this more clearly by running:

<pre>
$ firewall-cmd --get-default-zone
public
</pre> 


<h2>Changing Firewalld settings</h2>

You can configure a zone's settings using firewall-cmd. firewall-cmd can either persistent and non-persistent changes. 

However note, when making persistent changes with firewall-cmd, it effectively just makes changes to the firewalld internal config files but doesn't load them in. So to activate a persistent change, you need to restart the firewalld service, so that it loads in the newly changed config files.  




Here's the "home" zone's info:

<pre>
[root@routingvm zones]# firewall-cmd --zone=home --list-all
home
  target: default
  icmp-block-inversion: no
  interfaces:
  sources:
  services: dhcpv6-client mdns samba-client ssh
  ports:
  protocols:
  masquerade: no
  forward-ports:
  sourceports:
  icmp-blocks:
  rich rules:
</pre>  

 

In both the "home" and the "public" zones shown above, there isn't any ip address restriction, however we can add one, like this:

<pre>
$ firewall-cmd --zone=public --add-source=192.168.50.0/24
success
</pre>

Now let's check that an ip range restriction has now been applied:

<pre>
$ firewall-cmd --zone=public --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8 enp0s9
  sources: 192.168.50.0/24
  services: dhcpv6-client ssh
  ports:
  protocols:
  masquerade: no
  forward-ports:
  sourceports:
  icmp-blocks:
  rich rules:

</pre>

However this change isn't persistent, and is reverted when the the firewalld service is restarted:


<pre>
$ systemctl restart firewalld.service
$ firewall-cmd --list-all --zone=public
public (default, active)
  interfaces: enp0s3
  sources:
  services: dhcpv6-client ssh
  ports:
  masquerade: no
  forward-ports:
  icmp-blocks:
  rich rules:
</pre>

To make this change persistant, you need to use the "--permenant" flag:

<pre>
$ firewall-cmd --zone=public --add-source=192.168.50.0/24 --permanent
success
</pre>

However this change will take affect after a firewalld service restart:


<pre>
$ firewall-cmd --list-all --zone=public
public (default, active)
  interfaces: enp0s3
  sources:
  services: dhcpv6-client ssh
  ports:
  masquerade: no
  forward-ports:
  icmp-blocks:
  rich rules:

$ systemctl restart firewalld.service
$
$ firewall-cmd --list-all --zone=public
public (default, active)
  interfaces: enp0s3
  sources: 192.168.50.0/24
  services: dhcpv6-client ssh
  ports:
  masquerade: no
  forward-ports:
  icmp-blocks:
  rich rules:
</pre>

Hence, using the "--permenant" setting is a bit like how systemctl's "enable", i.e. it only takes affect after a service restart. Therefore if you want to make a persistant change that also is active in the current firewalld.session, then you will need run the same firewall-cmd command twice, one without the --permenant option, and one with.  

Notice above that the httpd service is not currently permitted in this zone. This means that we'll get a connection timeout message when you try to open a web browser and access the vm's web server's homepage. 

To fix this issue, we need to add the appropriate services to the zone. Let's first see what services we have available:

<pre>
$ firewall-cmd --get-services
RH-Satellite-6 amanda-client bacula bacula-client dhcp dhcpv6 dhcpv6-client dns ftp high-availability <mark>http</mark> <mark>https</mark> imaps ipp ipp-client ipsec kerberos kpasswd ldap ldaps libvirt libvirt-tls mdns mountd ms-wbt mysql nfs ntp openvpn pmcd pmproxy pmwebapi pmwebapis pop3s postgresql proxy-dhcp radius rpc-bind samba samba-client smtp ssh telnet tftp tftp-client transmission-client vnc-server wbem-https
</pre>  

Now let's add the httpd related services to the zone:

<pre>
$ firewall-cmd --add-service=http --zone=public
success
$ firewall-cmd --add-service=http --zone=public --permanent
success
$ firewall-cmd <span style="letter-spacing:1.5px">-</span>-add-service=https
success
$ firewall-cmd --add-service=https --zone=public --permanent
success
$ firewall-cmd --list-all --zone=public
public (default, active)
  interfaces: enp0s3
  sources: 192.168.50.0/24
  services: dhcpv6-client <strong>http https</strong> ssh
  ports:
  masquerade: no
  forward-ports:
  icmp-blocks:
  rich rules:
</pre>
 
  
After that you should now be able to access the websites. 

Also instead of adding the http and https services to the zone, we could have added the ports, i.e.: 

<pre>
$ firewall-cmd --add-port=80/tcp --permanent --zone=public
success
$ firewall-cmd --add-port=443/tcp --zone=public
success
$ firewall-cmd --add-port=80/tcp --permanent --zone=public
success
$ firewall-cmd --add-port=443/tcp --permanent --zone=public
success
$ firewall-cmd --list-all
public (default)
  interfaces:
  sources:
  services: dhcpv6-client ssh
  ports: 443/tcp 80/tcp
  masquerade: no
  forward-ports:
  icmp-blocks:
  rich rules:
</pre>


Now in the context of firewalld, a service is something that is associated to a protocol and port, and it is not the traditional services such as httpd, sshd,...etc. Due to this new definition, a service can essentially be represented in the form of a an xml file.  You can view a list of the default services by looking in the following directory:


<pre>
$ ls /usr/lib/firewalld/services/
amanda-client.xml  ftp.xml                ipsec.xml        mdns.xml     pmcd.xml        radius.xml        tftp-client.xml
bacula-client.xml  high-availability.xml  kerberos.xml     mountd.xml   pmproxy.xml     rpc-bind.xml      tftp.xml
bacula.xml         https.xml              kpasswd.xml      ms-wbt.xml   pmwebapis.xml   samba-client.xml  transmission-client.xml
dhcpv6-client.xml  http.xml               ldaps.xml        mysql.xml    pmwebapi.xml    samba.xml         vnc-server.xml
dhcpv6.xml         imaps.xml              ldap.xml         nfs.xml      pop3s.xml       smtp.xml          wbem-https.xml
dhcp.xml           ipp-client.xml         libvirt-tls.xml  ntp.xml      postgresql.xml  ssh.xml
dns.xml            ipp.xml                libvirt.xml      openvpn.xml  proxy-dhcp.xml  telnet.xml
</pre>

You can use one of these xml files as a template to create your own custom services (i.e. xml file), however your service does not go inside the above folder, instead it has to be placed in <code>/etc/firewalld/services</code> folder. Here's an example of what one of these xml files looks like:

<pre>
$ cat /usr/lib/firewalld/services/ssh.xml
<?xml version="1.0" encoding="utf-8"?>
<service>
  <short>SSH</short>
  <description>Secure Shell (SSH) is a protocol for logging into and executing commands on remote machines. It provides secure encrypted communications. If you plan on accessing your machine remotely via SSH over a firewalled interface, enable this option. You need the openssh-server package installed for this option to be useful.</description>
  <strong><port protocol="tcp" port="22"/></strong>
</service>
</pre>

In the world of firewalld, this xml file is referred to as a "service" because it associates a name (which in this case is name, ssh) to a protocol and a port number. 


<h2>Using firewall-config</h2>

Finally you can do all of the above using the <strong>firewall-config</strong> GUI tool. From the terminal we run:

<pre>
$ firewall-config
</pre> 

<a href="http://codingbee.net/wp-content/uploads/2015/06/XQadD0h.png"><img src="https://codingbee.net/wp-content/uploads/2015/06/XQadD0h.png" alt="" width="895" height="581" class="alignnone size-full wp-image-4768" /></a>

Useful Tip: you can make the changes using firewall-cmd, and then check whether the changes are what you expected by viewing the firewall-config gui.  




See also:

http://www.tecmint.com/configure-firewalld-in-centos-7/