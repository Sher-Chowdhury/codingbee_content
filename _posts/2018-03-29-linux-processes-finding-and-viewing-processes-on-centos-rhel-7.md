---
ID: 370
post_title: 'Linux Processes &#8211; Finding and Viewing Processes on CentOS/RHEL 7'
author: sher
post_excerpt: >
  In linux there are lots of processes
  running, there are a number of commands
  available to find and view these
  processes.
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/linux-processes-finding-and-viewing-processes-on-centos-rhel-7
published: true
post_date: 2018-03-29 00:00:00
---
In linux there are lots of processes running, there are a number of commands available to find and view these processes. To get a full list, we use the ps command:


The following command gives a complete list of all the processes that are currently running:

<pre>
$ ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.4  59744  4848 ?        Ss   May01   0:05 /usr/lib/systemd/systemd --switched-root --system --deserialize 24
root         2  0.0  0.0      0     0 ?        S    May01   0:00 [kthreadd]
root         3  0.0  0.0      0     0 ?        S    May01   0:00 [ksoftirqd/0]
root         5  0.0  0.0      0     0 ?        S<   May01   0:00 [kworker/0:0H]
.
.
...etc
</pre>


This lists (a)ll processes for every (u)ser and it lifts the “must have tty” restri(x)ion so to also allowing to display processes owned by system accounts (e.g. processes owned by the sshd service).


If you see the man page for ps, you'll find that the "aux" is bsd syntax, which isn't recommended. Instead we should be using the "standard syntax" alternative for specifying flags:


<pre>
$ ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 May01 ?        00:00:05 /usr/lib/systemd/systemd --switched-root --system --deserialize 24
root         2     0  0 May01 ?        00:00:00 [kthreadd]
.
.
.
</pre>


This lists the same processes, but gives different column info when compared to the aux, you can fix this by manually specifying what column you want:

<pre>
$ ps -eo user,pid,pcpu,pmem,vsize,rss,tname,stat,start_time,bsdtime,command | head
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.4  59744  4848 ?        Ss   May01   0:05 /usr/lib/systemd/systemd --switched-root --system --deserialize 24
root         2  0.0  0.0      0     0 ?        S    May01   0:00 [kthreadd]
root         3  0.0  0.0      0     0 ?        S    May01   0:00 [ksoftirqd/0]
root         5  0.0  0.0      0     0 ?        S<   May01   0:00 [kworker/0:0H]
root         6  0.0  0.0      0     0 ?        S    May01   0:00 [kworker/u2:0]
root         7  0.0  0.0      0     0 ?        S    May01   0:00 [migration/0]
root         8  0.0  0.0      0     0 ?        S    May01   0:00 [rcu_bh]
root         9  0.0  0.0      0     0 ?        S    May01   0:00 [rcuob/0]
root        10  0.0  0.0      0     0 ?        R    May01   0:04 [rcu_sched]
</pre>

You can find info about all the columns in the ps command's man page. Here's some one liner descriptions for some of the more less obvious columns:

<strong>VSZ</strong> - This is the amount of ram space that this process has reserved. 
<strong>RSS</strong> - This is the amount of ram that this process is actually using. 
<strong>TTY</strong> - A "?" indicates that this is process is not a shell job, i.e. it is a background process that has been triggered by a service, or parent processes
<strong>STAT</strong> - this means status, where "S" means it is asleep, and "R" means it is running. 


<h2>Parent and Child process</h2>
Processes can trigger other child processes. You can find the parent process id for a given process by displaying the parent process id "ppid" column:

<pre>
[root@localhost /]# ps -eo user,pid,ppid,pcpu,pmem,vsize,rss,tname,stat,start_time,bsdtime,command | head
USER       PID  PPID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1     0  0.0  0.4  59744  4848 ?        Ss   May01   0:05 /usr/lib/systemd/systemd --switched-root --system --deserialize 24
root         2     0  0.0  0.0      0     0 ?        S    May01   0:00 [kthreadd]
root         3     2  0.0  0.0      0     0 ?        S    May01   0:00 [ksoftirqd/0]
root         5     2  0.0  0.0      0     0 ?        S<   May01   0:00 [kworker/0:0H]
root         6     2  0.0  0.0      0     0 ?        S    May01   0:00 [kworker/u2:0]
root         7     2  0.0  0.0      0     0 ?        S    May01   0:00 [migration/0]
root         8     2  0.0  0.0      0     0 ?        S    May01   0:00 [rcu_bh]
root         9     2  0.0  0.0      0     0 ?        S    May01   0:00 [rcuob/0]
root        10     2  0.0  0.0      0     0 ?        R    May01   0:04 [rcu_sched]
</pre>


However this isn't that easy to following. A better command to use is the pstree, which shows the relationships more visually:



<pre>
$ pstree -ap
systemd,1 --switched-root --system --deserialize 24
  ├─ModemManager,626
  │   ├─{ModemManager},635
  │   └─{ModemManager},691
  ├─NetworkManager,2112 --no-daemon
  │   ├─dhclient,11814 -d -q -sf /usr/libexec/nm-dhcp-helper -pf /var/run/dhclient-enp0s3.pid -
  ├─at-spi-bus-laun,3794
  │   ├─dbus-daemon,3798 --config-file=/etc/at-spi2/accessibility.conf --nofork --print-address 3
  │   │   └─{dbus-daemon},3800
  │   ├─{at-spi-bus-laun},3795
  │   ├─{at-spi-bus-laun},3797
  │   └─{at-spi-bus-laun},3799
  ├─at-spi2-registr,3803 --use-gnome-session
  │   └─{at-spi2-registr},3806
  ├─atd,1332 -f
  ├─gdm,1334
  │   ├─gdm-simple-slav,1355 --display-id /org/gnome/DisplayManager/Displays/_0
  │   │   ├─Xorg,1367 :0 -background none -verbose -auth /run/gdm/auth-for-gdm-AS4ueL/database -seat seat0 -nolisten tcp vt1
  │   │   ├─gdm-session-wor,3593
  │   │   │   ├─gnome-session,3626 --session gnome-classic
  │   │   │   │   ├─abrt-applet,4015
  │   │   │   │   │   └─{abrt-applet},4035
  │   │   │   │   ├─gnome-settings-,3816
  │   │   │   │   │   ├─{gnome-settings-},3824
  │   │   │   │   │   ├─{gnome-settings-},3829
.
.
...etc
</pre>

You can also view the process visually using ps, like this:


<pre>
$ ps -ef f
UID        PID  PPID  C STIME TTY      STAT   TIME CMD
root         2     0  0 12:09 ?        S      0:00 [kthreadd]
root         3     2  0 12:09 ?        S      0:00  \_ [ksoftirqd/0]
root         5     2  0 12:09 ?        S<     0:00  \_ [kworker/0:0H]
root         7     2  0 12:09 ?        S      0:00  \_ [migration/0]
root         8     2  0 12:09 ?        S      0:00  \_ [rcu_bh]
root         9     2  0 12:09 ?        S      0:00  \_ [rcu_sched]
root        10     2  0 12:09 ?        S      0:00  \_ [watchdog/0]
.
.
.
.
...etc
</pre>



Note if a child process is note responding for any reason, then a quick way to fix this is by killing the parent process.   



One of the things you will often find yourself doing is locating a particular process. This can be done by piping the output of "ps -ef" to a grep command. However you can also use the pgrep command. For example to list all processes called something like "http" and are running under the user "apache", we do: 

<pre>
$ pgrep -lu apache http
20428 httpd
20429 httpd
20430 httpd
20431 httpd
20432 httpd

</pre>



[post-content post_name=rhsca-quiz]

[accordion]
[toggle title="What is the command to list all processes running on the machine?"]
$ ps -ef
# this list's (e)very process in (f)ull format
[/toggle]
[toggle title="What command can visually show process dependencies, along with pid values and command line arguements?"]
$ pstree -ap
# this will show all (p)id values and command line (a)rguements
[/toggle]
[toggle title="What is the command to display all the pids for processes matching the search term 'httpd' that are running under the user 'apache'?"]
$ pgrep -lu apache httpd
# this will (l)ist pids along with process names that are running by the given (u)ser
[/toggle]
[toggle title="What is the command to display all processes along with pid values for the root user?"]
$ pgrep -lu root
# this will (l)ist pids along with process names that are running by the given (u)ser
[/toggle]
[/accordion]