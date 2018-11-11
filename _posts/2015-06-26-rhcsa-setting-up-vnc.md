---
ID: 436
post_title: 'RHCSA &#8211; Setting up VNC'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-setting-up-vnc
published: true
post_date: 2015-06-26 00:00:00
---
VNC is a way to access a machine's gui desktop interface remotely. 

There are a few steps involved in setting this up, starting with installing the vnc software:

<h2>Installing vnc</h2>

The first you need to is install the the following:


<pre>
$ yum install tigervnc tigervnc-server
</pre>


<h2>Set the vncpasswd</h2>
Switch to the user you are going to give remote desktop access to:


<pre>
$ su - mchowdhury
</pre>

Now we need to set a "vnc password", and then return back to root user:


<pre>
[mchowdhury@localhost ~]$ vncpasswd
Password:
Verify:
[mchowdhury@localhost ~]$ exit
logout
</pre>


 
<h2>Create a "vncserver@:{x}.service"</h2>

Navigate to the following directory:


<pre>
$ cd /usr/lib/systemd/system
</pre>


In this folder you should find a file called "vncserver@.service":


<pre>
$ ll /usr/lib/systemd/system | grep vnc
-rw-r--r--. 1 root root 1744 Jun 10  2014 vncserver@.service
</pre>


The content of this file is:


[html]
$ cat vncserver@.service
# The vncserver service unit file
#
# Quick HowTo:
# 1. Copy this file to /etc/systemd/system/vncserver@:&lt;display&gt;.service
# 2. Edit &lt;USER&gt; and vncserver parameters appropriately
#   (&quot;runuser -l &lt;USER&gt; -c /usr/bin/vncserver %i -arg1 -arg2&quot;)
# 3. Run `systemctl daemon-reload`
# 4. Run `systemctl enable vncserver@:&lt;display&gt;.service`
#
# DO NOT RUN THIS SERVICE if your local area network is
# untrusted!  For a secure way of using VNC, you should
# limit connections to the local host and then tunnel from
# the machine you want to view VNC on (host A) to the machine
# whose VNC output you want to view (host B)
#
# [user@hostA ~]$ ssh -v -C -L 590N:localhost:590M hostB
#
# this will open a connection on port 590N of your hostA to hostB's port 590M
# (in fact, it ssh-connects to hostB and then connects to localhost (on hostB).
# See the ssh man page for details on port forwarding)
#
# You can then point a VNC client on hostA at vncdisplay N of localhost and with
# the help of ssh, you end up seeing what hostB makes available on port 590M
#
# Use &quot;-nolisten tcp&quot; to prevent X connections to your VNC server via TCP.
#
# Use &quot;-localhost&quot; to prevent remote VNC clients connecting except when
# doing so through a secure tunnel.  See the &quot;-via&quot; option in the
# `man vncviewer' manual page.


[Unit]
Description=Remote desktop service (VNC)
After=syslog.target network.target

[Service]
Type=forking
# Clean any existing files in /tmp/.X11-unix environment
ExecStartPre=/bin/sh -c '/usr/bin/vncserver -kill %i &gt; /dev/null 2&gt;&amp;1 || :'
ExecStart=/sbin/runuser -l &lt;USER&gt; -c &quot;/usr/bin/vncserver %i&quot;
PIDFile=/home/&lt;USER&gt;/.vnc/%H%i.pid
ExecStop=/bin/sh -c '/usr/bin/vncserver -kill %i &gt; /dev/null 2&gt;&amp;1 || :'

[Install]
WantedBy=multi-user.target
[/html]

This file is actually a template, and isn't used directly. The only part of this file that you will need to substitute are the 2 <code><user></code> placeholders that are near the bottom of this file. .  

Now you need to use this file as a template to create a new file which has the filename convention of, "vncserver@:{x}.service", where "x" is a number that is at least equal to 1:


<pre>
[root@localhost system]# cp vncserver@.service vncserver@\:1.service
[root@localhost system]# ls -l vncserver*
-rw-r--r--. 1 root root 1744 Jun 26 19:08 vncserver@:1.service
-rw-r--r--. 1 root root 1744 Jun 10  2014 vncserver@.service
</pre>
  
Notice the "\", that is so to treat the ":" as a literal string, otherwise it will fail to set the filename. 

Now replace the placeholders with the username of an existing linux user (which is listed in /etc/passwd). 


<h2>Start and enable the new vnc service</h2>

Next we reload all the daemons:

<pre>
$ systemctl daemon-reload
</pre>

This is to make systemd aware of the new .service file. 

Now let's check our new service's status:



<pre>
$ systemctl status vncserver@\:1.service
vncserver@:1.service - Remote desktop service (VNC)
   Loaded: loaded (/usr/lib/systemd/system/vncserver@:1.service; disabled)
   Active: inactive (dead)
</pre>

From this we can see that we need to start the service and enable it so that it auto-starts at next boot time:


<pre>
$ systemctl status vncserver@\:1.service
vncserver@:1.service - Remote desktop service (VNC)
   Loaded: loaded (/usr/lib/systemd/system/vncserver@:1.service; disabled)
   Active: inactive (dead)
</pre>
 

<pre>
$ systemctl start vncserver@\:1.service
</pre>

Note: we have to always escape the ":" with a backslash. 

Next we enable it:
<pre>
$ systemctl enable vncserver@\:1.service
ln -s '/usr/lib/systemd/system/vncserver@:1.service' '/etc/systemd/system/multi-user.target.wants/vncserver@:1.service'
</pre>

Now let's recheck the new service's status:


<pre>
$ systemctl status vncserver@\:1.service
vncserver@:1.service - Remote desktop service (VNC)
   Loaded: loaded (/usr/lib/systemd/system/vncserver@:1.service; <strong>enabled</strong>)
   Active: <strong>activ</strong>e (running) since Fri 2015-06-26 19:39:19 BST; 7min ago
 Main PID: 2795 (Xvnc)
   CGroup: /system.slice/system-vncserver.slice/vncserver@:1.service
           ‣ 2795 /usr/bin/Xvnc :1 -desktop localhost.localdomain:1 (mchowdhury) -auth /home/mchowdhury/.Xauthority -geometry 102...

Jun 26 19:39:16 localhost.localdomain systemd[1]: Starting Remote desktop service (VNC)...
Jun 26 19:39:19 localhost.localdomain systemd[1]: Started Remote desktop service (VNC).
</pre>

So far so good, next we need to sort out the firewall. 

<h2>Add service to firewalld zone</h2>

First let's locate the service that need to be added to the zone:


<pre>
$ firewall-cmd --get-services
amanda-client bacula bacula-client dhcp dhcpv6 dhcpv6-client dns ftp high-availability http https imaps ipp ipp-client ipsec kerberos kpasswd ldap ldaps libvirt libvirt-tls mdns mountd ms-wbt mysql nfs ntp openvpn pmcd pmproxy pmwebapi pmwebapis pop3s postgresql proxy-dhcp radius rpc-bind samba samba-client smtp ssh telnet tftp tftp-client transmission-client <strong>vnc-server</strong> wbem-https
</pre>

Now let's see if that has been already added to a zone:


<pre>
$ firewall-cmd --list-all
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

Add the moment, it hasn't been added yet, so let add it now:

<pre>
$ firewall-cmd --permanent --add-service=vnc-server
success
</pre>

Now let's recheck:


<pre>
$ firewall-cmd --list-all
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

Since we used the permanent option, it hasn't it for the current run time, but will appear the next time we reboot the machine. so let's do the next best thing to a reboot by restarting the firewalld service, then check again:

<pre>
$ systemctl restart firewalld
$ firewall-cmd --list-all
public (default, active)
  interfaces: enp0s3
  sources:
  services: dhcpv6-client ssh vnc-server
  ports:
  masquerade: no
  forward-ports:
  icmp-blocks:
  rich rules:
</pre>
 

<h2>Start a new vnc session</h2>
Let's now try out our new vnc setup by trying to start a remote connection.

During the connection, you will be prompted to enter 2 passwords, the first password is the machine login password. That's because the vnc session operates inside a ssh tunnel. And the second password is the vnc password. Here's the command we need to run:


<pre>$ vncviewer via mchowdhury@localhost localhost:1</pre>

The "localhost:1" specifies which vnc service we want to connect to, which matches the number specified in the .service file's filename. 

You may then get prompted to enter the user's main machine password. 

You should then get a pop window prompting you to enter the user's vnc password, then then finally a new pop up window opens with the remote gui interface.