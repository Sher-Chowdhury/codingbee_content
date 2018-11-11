---
ID: 2754
post_title: >
  Setting up an Apache Web Server (httpd)
  on CentOS/RHEL 7
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/open-source-software/apache-web-server/setting-up-an-apache-web-server-httpd-on-centos
published: true
post_date: 2018-03-03 11:47:56
---
In this series of articles we're going to set up an Apache Web Server and walkthrough the various Apache configurations and features. To start with this article will cover setting up a basic Apache server with the default out-of-the-box apache configurations. 

You can follow along with this <a href="https://github.com/Sher-Chowdhury/CentOS7-Apache-Web-Server-demo">Apache Vagrant project</a>. This vagrant project is made up of 2 CentOS7 boxes, one box will act as our webserver (webserver.local - 10.0.5.10) and the other will act as our client (box1.local - 10.0.5.11).


  
We will also ensure: 

<ol>
	<li>firewalld is running and not blocking web server traffic</li>
	<li>SELinux is in targeted mode</li>
</ol>

But for this walkthrough we will initially keep them turned off:

<pre lang="bash">
[root@webserver ~]# systemctl stop firewalld
[root@webserver ~]# setenforce Permissive
</pre>






<h2>Installing Apache Software</h2>


This is done using yum to install httpd:

<pre lang='bash'>
[root@webserver ~]# yum install httpd
</pre>


This installs httpd with the minimal set of dependant rpms. This installation will install the minimal software to give you the most essential web server software. However you might run into problems when you try to some less common use cases, which is why a better approach is to install httpd as part of a suite of web server related packages by doing a yum groupinstall:






<pre lang='bash'>
[root@webserver ~]# yum group list | grep -i 'web'
There is no installed groups file.
Maybe run: yum groups mark convert (see man yum)
   <strong>Basic Web Server</strong>
   Web-Based Enterprise Management
</pre>

Now to install it we we:

<pre>
[root@webserver ~]# yum group install 'Basic Web Server'
</pre>

This approach will end up installing a lot more packages, such as library packages. This will help avoid troubleshooting for missing rpms later down the road

On the client box We'll install some additional tools that are used for testing and troubleshooting issues, First we'll install a couple of terminal based web browser:

<pre>
[root@box1 ~]# yum install elinks lynx
</pre>

And we'll also install nc, which is used for testing network connectivity:

<pre>
[root@box1 ~]# yum install nc
</pre>








<h2>Starting the Web Server Daemon</h2>

We will keep the default apache configuration for now, and we'll just start the web server service. You can do this either using systemctl or apachectl (they both do the same thing behind the scenes):

<pre>
$ systemctl start httpd

# or

$ apachectl start

</pre>


We will cover apachectl command in more detail later, but for now we'll stick to using systemctl. Next we make httpd deamon persistant:


<pre>
$ systemctl enable httpd
Created symlink from /etc/systemd/system/multi-user.target.wants/httpd.service to /usr/lib/systemd/system/httpd.service.
</pre>


Now let's check the httpd status:


<pre lang="bash">
[root@target ~]# systemctl status httpd
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; vendor preset: disabled)
   Active: active (running) since Sat 2018-03-03 12:31:04 UTC; 12min ago
     Docs: man:httpd(8)
           man:apachectl(8)
  Process: 6332 ExecStop=/bin/kill -WINCH ${MAINPID} (code=exited, status=0/SUCCESS)
 Main PID: 6347 (httpd)
   Status: "Total requests: 0; Current requests/sec: 0; Current traffic:   0 B/sec"
   CGroup: /system.slice/httpd.service
           ├─6347 /usr/sbin/httpd -DFOREGROUND
           ├─6348 /usr/sbin/httpd -DFOREGROUND
           ├─6349 /usr/sbin/httpd -DFOREGROUND
           ├─6350 /usr/sbin/httpd -DFOREGROUND
           ├─6351 /usr/sbin/httpd -DFOREGROUND
           ├─6352 /usr/sbin/httpd -DFOREGROUND
           └─6353 /usr/sbin/httpd -DFOREGROUND

Mar 03 12:31:04 target.cb.net systemd[1]: Starting The Apache HTTP Server...
Mar 03 12:31:04 target.cb.net systemd[1]: Started The Apache HTTP Server.
</pre>

So far so good. 


Now let's check which ports this deamon is listening on:


<pre>
$ ss -nutlp | grep -i http
tcp    LISTEN     0      128      :::80                   :::*                   users:(("httpd",pid=26598,fd=4),("httpd",pid=26590,fd=4),("httpd",pid=6353,fd=4),("httpd",pid=6352,fd=4),("httpd",pid=6351,fd=4),("httpd",pid=6350,fd=4),("httpd",pid=6349,fd=4),("httpd",pid=6348,fd=4),("httpd",pid=6347,fd=4))
tcp    LISTEN     0      128      :::443                  :::*                   users:(("httpd",pid=26598,fd=6),("httpd",pid=26590,fd=6),("httpd",pid=6353,fd=6),("httpd",pid=6352,fd=6),("httpd",pid=6351,fd=6),("httpd",pid=6350,fd=6),("httpd",pid=6349,fd=6),("httpd",pid=6348,fd=6),("httpd",pid=6347,fd=6))
</pre>

Here it shows that the our web server daemon is listening on port 80 and 443. A further check you can do is this:

<pre>
[root@webserver httpd]# fuser -v -n tcp 80
                     USER        PID ACCESS COMMAND
80/tcp:              root       5683 F.... httpd
                     apache     5684 F.... httpd
                     apache     5685 F.... httpd
                     apache     5686 F.... httpd
                     apache     5687 F.... httpd
                     apache     5688 F.... httpd
                     apache     5689 F.... httpd
[root@webserver httpd]# fuser -v -n tcp 443
                     USER        PID ACCESS COMMAND
443/tcp:             root       5683 F.... httpd
                     apache     5684 F.... httpd
                     apache     5685 F.... httpd
                     apache     5686 F.... httpd
                     apache     5687 F.... httpd
                     apache     5688 F.... httpd
                     apache     5689 F.... httpd
</pre>


Let's now confirm that our client can connect to these ports:


<pre>
[root@box1 ~]# nc -v 10.0.5.10 80
Ncat: Version 6.40 ( http://nmap.org/ncat )
Ncat: Connected to 10.0.5.10:80.
^C
[root@box1 ~]# nc -v 10.0.5.10 443
Ncat: Version 6.40 ( http://nmap.org/ncat )
Ncat: Connected to 10.0.5.10:443.
^C
</pre> 

We can now try to access the home page: 


<pre lang='bash'>
[root@webserver httpd]# curl http://10.0.5.10 | head
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  4897  100  4897    0     0   801k      0 --:--:-- --:--:-- --:--:--  956k
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1//EN" "http://www.w3.org/TR/xhtml11/DTD/xhtml11.dtd"><html><head>
<meta http-equiv="content-type" content="text/html; charset=UTF-8">
		<title>Apache HTTP Server Test Page powered by CentOS</title>

</pre>

This is basically the html code generic home page that comes with a new install of the httpd package. For a nicer view of this code use either elinks or lynx: 

<pre>
[root@box1 ~]# elinks http://10.0.5.10
</pre>

Note, elinks doesn't support https. So test connection via port 443, you need to do this by using either curl or lynx:


<pre>
[root@box1 ~]# curl -k https://10.0.5.10 | head

# or 

[root@box1 ~]# lynx https://10.0.5.10
</pre>

We can replace the generic welcome page with a custom one by creating the following file:

<pre>
[root@webserver ~]# echo 'Hello CodingBee' > /var/www/html/index.html
</pre>

Now let's test this:

<pre>
[root@box1 ~]# curl -k http://10.0.5.10
Hello CodingBee
[root@box1 ~]# curl -k https://10.0.5.10
Hello CodingBee
</pre>



<h2>Setup Firewalld to allow http traffic</h2>

Now let's activate firewalld:

<pre>
$ systemctl start firewalld
$ systemctl enable firewalld
</pre>

Now let's see what traffic is permitted:


<pre>
$ firewall-cmd list-all
usage: see firewall-cmd man page
firewall-cmd: error: unrecognized arguments: list-all
[root@target ~]# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8
  sources:
<strong>  services: ssh dhcpv6-client</strong>
  ports: 3260/tcp
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
</pre>

At the moment only ssh and dhcpv6-client is whitelisted for the enp0s3 and enp0s8 network adapters. enp0s8 happens to be the adapter that our client uses to connect to the web server:

<pre>
[root@webserver ~]# ip addr show enp0s8
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN qlen 1000
    link/ether 08:00:27:51:10:c7 brd ff:ff:ff:ff:ff:ff
    inet 10.0.5.10/24 brd 10.0.5.255 scope global enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe51:10c7/64 scope link
       valid_lft forever preferred_lft forever

</pre>




This means our client has now lost access to port 80 and 443:

<pre>
[root@box1 ~]# nc -v 10.0.5.10 80
Ncat: Version 6.40 ( http://nmap.org/ncat )
Ncat: No route to host.
[root@box1 ~]# nc -v 10.0.5.10 443
Ncat: Version 6.40 ( http://nmap.org/ncat )
Ncat: No route to host.


# consequently it means curling will also fail:

[root@box1 ~]# curl -k http://10.0.5.10
curl: (7) Failed connect to 10.0.5.10:80; No route to host
[root@box1 ~]# curl -k https://10.0.5.10
curl: (7) Failed connect to 10.0.5.10:443; No route to host
</pre>



So let's now add http (port 80) and https (443) to the whitelist:


<pre>
[root@webserver ~]# firewall-cmd --add-service=http
success
[root@webserver ~]# firewall-cmd --add-service=https
success

[root@webserver ~]# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8
  sources:
  services: ssh dhcpv6-client <strong>http https</strong>
  ports: 3260/tcp
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
</pre>


Now we can confirm that our client has access again: 

<pre>
[root@box1 ~]# nc -v 10.0.5.10 80
Ncat: Version 6.40 ( http://nmap.org/ncat )
Ncat: Connected to 10.0.5.10:80.
^C
[root@box1 ~]# nc -v 10.0.5.10 443
Ncat: Version 6.40 ( http://nmap.org/ncat )
Ncat: Connected to 10.0.5.10:443.
^C
</pre>

This means we can access the homepage again using curl/elinks/lynx, e.g.:

<pre>
[root@box1 ~]# curl -k http://10.0.5.10
Hello CodingBee
</pre> 



Now that we have confirmed that or firewalld is working as expected, we now need to make them persistant:

<pre>
[root@webserver ~]# firewall-cmd --runtime-to-permanent
success
</pre>



<h2>SELinux setup</h2>

When it comes to Apache and SELinux, there's a lot of stuff to consider, such as sebooleans, file contexts, using non-standard ports. The following documentation provides a lot of information about these:


<pre>
$ man httpd_selinux
</pre>

This documentation is available as part of the <strong>selinux-policy-doc</strong> rpm. 




Let's now turn on SELinux:


<pre>
[root@webserver ~]# setenforce Enforcing
[root@webserver ~]# getenforce
Enforcing
</pre>

This has made zero difference on the client side:

<pre>
[root@box1 ~]# curl -k http://10.0.5.10
Hello CodingBee
[root@box1 ~]# curl -k https://10.0.5.10
Hello CodingBee
</pre>

That's because SELinux has a predefined set of Apache specific policies that supports the out-of-the box default httpd configurations. However there are lots of gotchas to watch out for, we'll cover them in the upcoming Apache articles.