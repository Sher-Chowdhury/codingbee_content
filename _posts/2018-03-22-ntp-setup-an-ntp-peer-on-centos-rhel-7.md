---
ID: 3129
post_title: 'NTP &#8211; Setup an NTP Server on CentOS/RHEL 7'
author: sher
post_excerpt: "Here we'll cover how to setup an ntp server that's using it's own system time as an accurate time provider, and also setup an ntp peer. "
layout: post
permalink: >
  https://codingbee.net/tutorials/rhce/ntp-setup-an-ntp-peer-on-centos-rhel-7
published: true
post_date: 2018-03-22 16:10:41
---
An NTP server is a server that provides time information to NTP clients. An <strong>NTP peer</strong> is a server that provides and receives time from other NTP servers. So you can have a group of NTP peers that provides time information to eachother. An <strong>NTP client</strong> is a server that only receives time information from NTP servers to keep it's own time in sync. 


We can setup an NTP server, so that it's local system clock (as provided by the date or timedatectl command). We can refer to this type of server as a 'local time NTP server' and it  will provide time information to NTP clients. This is good solution for private network where it's more important to have in-sync time rather than super accurate time. So let's setup a local time NTP server. We will set this up using the ntpd deamon (an alternative to ntpd daemon is to use chrony). We have created a <a href="https://github.com/Sher-Chowdhury/CentOS7-NTP-demo">vagrant ntp project on github</a> to help you follow along with this demo.  


<h2>Setup a local time NTP server</h2>



First install the ntp rpm:

<pre>
$ yum install ntp
</pre>


In the /etc/ntp.conf comment out the existing server entries and insert the following line:


<pre>
$ grep 'server' /etc/ntp.conf
# Use public servers from the pool.ntp.org project.
#server 0.centos.pool.ntp.org iburst
#server 1.centos.pool.ntp.org iburst
#server 2.centos.pool.ntp.org iburst
#server 3.centos.pool.ntp.org iburst
#broadcast 192.168.1.255 autokey	# broadcast server
#broadcast 224.0.1.1 autokey		# multicast server
#manycastserver 239.255.254.254		# manycast server
<strong>server 127.127.1.0</strong>
</pre>

Here, the <code>127.127.1.0</code> is actually a special reserved IP address that used to instruct NTP to use it's own system clock as a provider of accurate time.  


Next we need update firewalld to allow incoming time requests from ntp clients:

<pre>
$ firewall-cmd --permanent --add-service=ntp
success
$ systemctl restart firewalld.service
</pre>


Now start and enable the ntpd daemon:

<pre>
$ systemctl start ntpd
$ systemctl enable ntpd
</pre>


You can then check if ntpd daemon is listening on it's port:

<pre>
$ ss -atun | grep 123
</pre>
Note: ntp listens on a udp port, not tcp port.


Then enable time syncing:


<pre>
$ timedatectl set-ntp true
</pre>


Now we can check if this has worked by running:

<pre>
$ ntpq -p
</pre>

and to get synchronisation performance we do:

<pre>
$ ntpstat
</pre>



<h2>Setup an NTP Peer</h2>

Now that we have setup NTP server using it's own system clock as it's reference. We can now create an NTP peer to connect to it. 

The steps involved is identical to setting up the NTP server. The only difference is that instead of inserting a Server line, we insert a 'peer' line which references our new NTP server's ip address (which in our example is 10.2.4.10):


<pre>
$ echo 'peer {local-time-server-ip-address}' >> /etc/ntp.conf
</pre>


We can then test the setups by running:

<pre>
$ ntpq -c lpeer
     remote           refid      st t when poll reach   delay   offset  jitter
==============================================================================
*ntp-server.exam LOCAL(0)         6 u    5   64   17    0.558  -46.497  31.817
</pre>

The '*' at the start of the line indicates that syncing is in progress. 

we can also test by running:



<pre>
[root@ntp-peer ~]# ntpstat
<strong>unsynchronised</strong>
   polling server every 64 s
</pre>

Notice it shows as unsynchronised. Also you might see:


<pre>
[root@ntp-peer ~]# timedatectl
      Local time: Thu 2018-03-22 16:24:09 UTC
  Universal time: Thu 2018-03-22 16:24:09 UTC
        RTC time: Thu 2018-03-22 16:24:09
       Time zone: UTC (UTC, +0000)
     NTP enabled: yes
<strong>NTP synchronized: no</strong>
 RTC in local TZ: no
      DST active: n/a
</pre>


If that is so, then you might need to wait about 30 mins before it shows:

<pre>
[root@ntp-peer ~]# ntpstat
synchronised to NTP server (10.2.4.10) at stratum 7
   time correct to within 19 ms
   polling server every 64 s
</pre>

and: 


<pre>[root@ntp-peer ~]# timedatectl
      Local time: Thu 2018-03-22 16:51:36 UTC
  Universal time: Thu 2018-03-22 16:51:36 UTC
        RTC time: Thu 2018-03-22 16:51:35
       Time zone: UTC (UTC, +0000)
     NTP enabled: yes
NTP synchronized: yes
 RTC in local TZ: no
      DST active: n/a</pre>


[post-content post_name=rhsca-quiz] 

The following are questions about setting up an <strong>NTP server</strong>:
[accordion]
[toggle title="What is the command to install the time syncing software?"]
$ yum install ntp
[/toggle]
[toggle title="What entry needs to be added to the configs in order for ntp server to use it's own system time?"]
# Ensure the following line is present in the /etc/ntp.conf:
server 127.127.1.0
# also there are no other active server directives 
[/toggle]
[toggle title="What firewall related commands needs to be run?"]
$ firewall-cmd --permanent --add-service=ntp
success
$ systemctl restart firewalld.service
[/toggle]
[toggle title="what is the command to start+enable the time syncing daemon?"]
$ systemctl start ntpd
$ systemctl enable ntpd
[/toggle]
[toggle title="Now how do you start time syncing?"]
$ timedatectl set-ntp true
[/toggle]
[toggle title="What commands can you run to check if syncing is working?"]
$ ntpstat
# or 
$ ntpq -p
[/toggle]
[/accordion]

The following are questions about setting up an <strong>NTP Peer</strong>:

[accordion]
[toggle title="What are the steps to create an NTP peer?"]
It's the same as setting up an NTP server, except you don't have any active 'server' lines, and instead have a 'peer' line specify the ip address of another NTP server/peer. 
[/toggle]
[toggle title="What commands can you run to check if peering based time syncing is successful?"]
$ ntpstat
# it can take about 30 mins for syncing to complete....so be patient!!! 
[/toggle]
[/accordion]