---
ID: 435
post_title: 'NTP &#8211; Keeping System time in sync on CentOS/RHEL 7'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/ntp-keeping-system-time-in-sync-on-centos-rhel-7
published: true
post_date: 2018-03-19 00:00:00
---
There are two main clocks in your machine:

<ul>
	<li><strong>hardware clock</strong> - This clock is operated on your machine's motherboard</li>
	<li><strong>system-clock (aka system-time)</strong> - This is your operating system's clock</li>
</ul>




So when you start your machine, the first clock that your machine uses for timekeeping is the hardware clock, while the OS is booting up, the system-clock gets it's time from the hardware-clock. After that the system-clock's time get's maintained by the OS. 

You can take a look at the hardware clock using the hwclock command:

<pre>
$ hwclock
Wed 21 Mar 2018 11:29:46 AM UTC  -0.566718 seconds
</pre>

You can view the OS time using the <code>timedatectl</code> or <code>date</code> command:

<pre>
$ timedatectl
      Local time: Wed 2018-03-21 11:39:10 GMT
  Universal time: Wed 2018-03-21 11:39:10 UTC
        RTC time: Wed 2018-03-21 11:39:09
       Time zone: Europe/London (GMT, +0000)
     NTP enabled: yes
NTP synchronized: yes
 RTC in local TZ: no
      DST active: no
 Last DST change: DST ended at
                  Sun 2017-10-29 01:59:59 BST
                  Sun 2017-10-29 01:00:00 GMT
 Next DST change: DST begins (the clock jumps one hour forward) at
                  Sun 2018-03-25 00:59:59 GMT
                  Sun 2018-03-25 02:00:00 BST



$ date
Wed Mar 21 11:39:12 GMT 2018

</pre>

the date command doesn't do a lot, but it's useful for outputting the date+time in different formats. 

You can then change the system-clock's time manually by using the <strong>timedatectl</strong> command:

<pre>
$ date
Tue 23 Jun 02:32:32 BST 2018

$ timedatectl set-time 04:00

$ date
Tue 23 Jun 04:00:02 BST 2018
</pre>





<h2>Setting the timezone</h2>

The timedatecl shows which timezone the machine has been set to:



<pre>
$ timedatectl
      Local time: Wed 2018-03-21 11:39:10 GMT
  Universal time: Wed 2018-03-21 11:39:10 UTC
        RTC time: Wed 2018-03-21 11:39:09
       <strong>Time zone: Europe/London (GMT, +0000)</strong>
     NTP enabled: yes
NTP synchronized: yes
 RTC in local TZ: no
      DST active: no
 Last DST change: DST ended at
                  Sun 2017-10-29 01:59:59 BST
                  Sun 2017-10-29 01:00:00 GMT
 Next DST change: DST begins (the clock jumps one hour forward) at
                  Sun 2018-03-25 00:59:59 GMT
                  Sun 2018-03-25 02:00:00 BST
</pre>


However if you want to change this, then first use the <code>tzselect</code> command. This command is an interactive tool that work out what your time zone is called. Once you have the name, you can then set the timezone using the timedatectl command:


<pre>
$ timedatectl set-timezone Europe/London
</pre>



<h2>Time synchronisation</h2>

It's really important to keep your system's clock as accurate as possible in order to ensure the smooth running of your machine. You can keep your machine's clock accurate by using NTP (Network Time Protocol). There are 2 main NTP rpms availalable:

<ul>
	<li>ntp - recommended for machines that are constantly connected to a network and are normally running 24/7</li>
	<li>chrony - recommended for machines that are not running 24/7 or have intermitent network, e.g. mobile phones. Also performs well on machine that are constantly connected to a network and are normally running 24/7</li>
</ul>

Both of these do the the same job, which is to synchronise a system's time with another reference time source. You shouldn't use both. chrony is the successor to ntp and comes preinstalled in CentOS/RHEL 7. 


<h2>Install ntp</h2>
Here's the command you need to run:

<pre>
$ yum install chrony
</pre>



You can also use timedatectl to instruct your OS to accurately maintain the correct time by keeping it's time in sync with a another trusted remote "ntp" server. This is done by running the following command:

<pre>
$ timedatectl set-ntp yes
</pre>


We can view a list of trusted ntp servers that the chronyd is using to sync the system-time. You can view this list using the <strong>chronyc</strong> command: 

<pre>
$ chronyc sources -v
210 Number of sources = 4

  .-- Source mode  '^' = server, '=' = peer, '#' = local clock.
 / .- Source state '*' = current synced, '+' = combined , '-' = not combined,
| /   '?' = unreachable, 'x' = time may be in error, '~' = time too variable.
||                                                 .- xxxx [ yyyy ] +/- zzzz
||                                                /   xxxx = adjusted offset,
||         Log2(Polling interval) -.             |    yyyy = measured offset,
||                                  \            |    zzzz = estimated error.
||                                   |           |
MS Name/IP address         Stratum Poll Reach LastRx Last sample
===============================================================================
^? ns1.tomhek.net                3   9   377   208   -607us[ -607us] +/-   91ms
^+ ntp1.mediamatic.nl            2  10   377   399   +446us[ +446us] +/-   44ms
^* metronoom.dmz.cs.uu.nl        1   9   377   976   -298us[ -335us] +/-   22ms
^- mirror.muntinternet.net       2  10   377   126   +318us[ +318us] +/-   65ms

</pre>

(Note, the ntpd equivalent to this command is <code>ntpq -p</code>).The asterisk indicates the which one is being used as the primary server. The primary NTP server can change every few minutes based on chronyd's internal alorithm. To get more info about our ntp status, we run: 

<pre>
$ chronyc tracking
Reference ID    : 213.136.0.252 (ntp4.bit.nl)
Stratum         : 2
Ref time (UTC)  : Sun Oct  4 16:13:07 2015
System time     : 0.001208134 seconds fast of NTP time
Last offset     : 0.002128782 seconds
RMS offset      : 0.003163246 seconds
Frequency       : 500.079 ppm fast
Residual freq   : 7.099 ppm
Skew            : 37.938 ppm
Root delay      : 0.038595 seconds
Root dispersion : 0.000447 seconds
Update interval : 64.3 seconds
Leap status     : Normal

</pre>

(Note, the ntpd equivalent to this command is <code>ntpstat</code>.) This shows how effectively our ntp service is working to keep the time in sync with the remote ntp server. The main config file for our chronyd service is:


<pre>
$ cat /etc/chrony.conf
# Use public servers from the pool.ntp.org project.
# Please consider joining the pool (http://www.pool.ntp.org/join.html).
<strong>
server 0.centos.pool.ntp.org iburst
server 1.centos.pool.ntp.org iburst
server 2.centos.pool.ntp.org iburst
server 3.centos.pool.ntp.org iburst
</strong>
# Ignore stratum in source selection.
stratumweight 0

# Record the rate at which the system clock gains/losses time.
driftfile /var/lib/chrony/drift

# Enable kernel RTC synchronization.
rtcsync

# In first three updates step the system clock instead of slew
# if the adjustment is larger than 10 seconds.
makestep 10 3

# Allow NTP client access from local network.
#allow 192.168/16

# Listen for commands only on localhost.
bindcmdaddress 127.0.0.1
bindcmdaddress ::1

# Serve time even if not synchronized to any NTP server.
#local stratum 10

keyfile /etc/chrony.keys

# Specify the key used as password for chronyc.
commandkey 1

# Generate command key if missing.
generatecommandkey

# Disable logging of client accesses.
noclientlog

# Send a message to syslog if a clock adjustment is larger than 0.5 seconds.
logchange 0.5

logdir /var/log/chrony
#log measurements statistics tracking
</pre>

The <code>server</code> settings are the important part of this file. These servers, e.g. 0.centos.pool.ntp.org are NTP servers which can be used as an accurate time source that chronyd will use to keep sync with. If you want to do a manual time resync, you can simply do this by restarting the ntp service:


<pre>
$ systemctl restart chronyd
</pre>




If your system clock is correct, but your hardware-clock is wrong, then you can update the hardware clock using the <strong>hwclock</strong> command:

<pre>
$ hwclock --systohc
</pre>


Also to updated the system clock so that it stays insync with the time provided via chronyd/ntpd deamon, we run:


<pre>
$ timedatectl set-ntp true
</pre>


then behind the scenes it will end up starting/stopping the <strong>chronyd</strong> service:


<pre>
$ systemctl status chronyd
chronyd.service - NTP client/server
   Loaded: loaded (/usr/lib/systemd/system/chronyd.service; enabled)
   Active: active (running) since Tue 2015-06-23 04:31:35 BST; 2 days ago
  Process: 9562 ExecStartPost=/usr/libexec/chrony-helper add-dhclient-servers (code=exited, status=0/SUCCESS)
  Process: 9556 ExecStart=/usr/sbin/chronyd -u chrony $OPTIONS (code=exited, status=0/SUCCESS)
 Main PID: 9560 (chronyd)
   CGroup: /system.slice/chronyd.service
           └─9560 /usr/sbin/chronyd -u chrony

Jun 23 04:31:35 localhost.localdomain chronyd[9560]: chronyd version 1.29.1 starting
Jun 23 04:31:35 localhost.localdomain chronyd[9560]: Linux kernel major=3 minor=10 patch=0
Jun 23 04:31:35 localhost.localdomain chronyd[9560]: hz=100 shift_hz=7 freq_scale=1.00000000 nominal_tick=10000 slew_delta_t...pll=2
Jun 23 04:31:35 localhost.localdomain chronyd[9560]: Frequency -9.935 +/- 3.661 ppm read from /var/lib/chrony/drift
Jun 23 04:31:35 localhost.localdomain systemd[1]: Started NTP client/server.
Jun 23 04:31:42 localhost.localdomain chronyd[9560]: Selected source 84.245.25.222
Jun 23 04:31:42 localhost.localdomain chronyd[9560]: System clock wrong by 227763.759989 seconds, adjustment started
Jun 25 19:47:46 localhost.localdomain chronyd[9560]: System clock was stepped by 227763.760 seconds
Jun 25 19:48:52 localhost.localdomain chronyd[9560]: Selected source 194.171.167.130
Hint: Some lines were ellipsized, use -l to show in full.
</pre>


The chronyd service has it's own config file is:



<pre>
$ cat /etc/chrony.conf
# Use public servers from the pool.ntp.org project.
# Please consider joining the pool (http://www.pool.ntp.org/join.html).
server 0.centos.pool.ntp.org iburst
server 1.centos.pool.ntp.org iburst
server 2.centos.pool.ntp.org iburst
server 3.centos.pool.ntp.org iburst

# Ignore stratum in source selection.
stratumweight 0

# Record the rate at which the system clock gains/losses time.
driftfile /var/lib/chrony/drift

# Enable kernel RTC synchronization.
rtcsync

# In first three updates step the system clock instead of slew
# if the adjustment is larger than 10 seconds.
makestep 10 3

# Allow NTP client access from local network.
#allow 192.168/16

# Listen for commands only on localhost.
bindcmdaddress 127.0.0.1
bindcmdaddress ::1

# Serve time even if not synchronized to any NTP server.
#local stratum 10

keyfile /etc/chrony.keys

# Specify the key used as password for chronyc.
commandkey 1

# Generate command key if missing.
generatecommandkey

# Disable logging of client accesses.
noclientlog

# Send a message to syslog if a clock adjustment is larger than 0.5 seconds.
logchange 0.5

logdir /var/log/chrony
#log measurements statistics tracking

</pre>

But's it's unlikely you need to make any changes on this file, since the main way to configure your system clock is by using the <strong>timedatectl</strong> command. The only thing you may need to configure here is adding/removing ntp servers. 


<h2>On the Job advice</h2>

AWS provides a dedicated <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/set-time.html">free AWS NTP service</a> for it's EC2 instances. This avoids your ec2 instances needing to reach out to the internet for time syncing.



<h2>RHCSA quiz</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="How many different types of clocks exists on your machine and what are they called?"]
There are 2 types:
- Hardware clock
- System clock
</ul>
[/toggle]
[toggle title="What is the command to install the ntp service?"]
$ yum install chrony
[/toggle]
[toggle title="What is ntp's main config file?"]
/etc/chrony.conf   # the only thing you might need 
                   # to update here are the ntp servers. 
[/toggle]
[toggle title="What is the command to check the NTP service's status?"]
$ systemctl status chronyd.service
[/toggle]
[toggle title="What is command to view all your time related settings, e.g. current time, timezone, whether ntp is enabled, daylight saving time....etc?"]
$ timedatectl status
[/toggle]
[toggle title="What is the command to enable ntp?"]
$ timedatectl set-ntp true
[/toggle]
[toggle title="What is the command to view a list of ntp servers that your machine is using?"]
$ chronyc sources -v
[/toggle]
[toggle title="What is the command to do a manual ntp sync?"]
$ systemctl restart chronyd
[/toggle]
[toggle title="What is the command to check how effectively our ntp syncing is working?"]
$ chronyc tracking
[/toggle]
[toggle title="What is the command to update your hardware clock?"]
$ hwclock --systohc
[/toggle]
[/accordion]