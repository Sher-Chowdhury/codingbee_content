---
ID: 453
post_title: 'Systemd &#8211; Adjusting a unit&#8217;s settings'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-adjusting-a-units-settings
published: true
post_date: 2015-09-25 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="How do you change the httpd service's default 'WantedBy=multi-user.target' setting to 'WantedBy=graphical.target'"]
# First make a template
cp /usr/lib/systemd/system/httpd.service /etc/systemd/system/httpd.service
# Now remove all the things you don't want to override and 
# then apply the adjustment you want, so that you end up with: 
$ cat /etc/systemd/system/httpd.service
[Install] 
 
WantedBy=graphical.target
[/toggle]
[/accordion]

<hr/>


Let's say we want to adjust the httpd's service, e.g. make it dependent on the graphical.target, rather that multi-user.target. 


In that case let's first check the httpd service's status:

<pre>
$ systemctl status httpd.service
httpd.service - The Apache HTTP Server
   Loaded: loaded (<mark>/usr/lib/systemd/system/httpd.service</mark>; enabled)
   Active: active (running) since Sat 2015-06-06 11:08:52 BST; 5min ago
 Main PID: 4336 (httpd)
   Status: "Total requests: 0; Current requests/sec: 0; Current traffic:   0 B/sec"
   CGroup: /system.slice/httpd.service
           ├─4336 /usr/sbin/httpd -DFOREGROUND
           ├─4337 /usr/sbin/httpd -DFOREGROUND
           ├─4338 /usr/sbin/httpd -DFOREGROUND
           ├─4339 /usr/sbin/httpd -DFOREGROUND
           ├─4340 /usr/sbin/httpd -DFOREGROUND
           └─4341 /usr/sbin/httpd -DFOREGROUND

Jun 06 11:08:52 centos7.codingbee.dyndns.org systemd[1]: Starting The Apache HTTP Server...
Jun 06 11:08:52 centos7.codingbee.dyndns.org systemd[1]: Started The Apache HTTP Server.
</pre>


Here we can see which file needs that contains the setting we want to adjust.Another way to locate this file is:

<pre>
$ rpm -ql httpd | grep '.service'
/usr/lib/systemd/system/htcacheclean.service
/usr/lib/systemd/system/httpd.service
</pre>



The <code>/usr/lib/systemd/system</code> directory is where you find all your unit's along with their default settings. Unit files are usually dropped into this folder when you install rpm packages, e.g. the httpd package.

It's not best practice to manually edit any of these unit files in this directory. If you want to change any of these unit files, then you do this by overriding the settings. This is done by creating/editing unit files in the <code>/etc/systemd/system/</code> directory instead:

<pre>
$ ls -l /etc/systemd/system/
total 16
drwxr-xr-x. 2 root root   54 Mar 14 19:20 basic.target.wants
drwxr-xr-x. 2 root root   30 Mar 14 19:18 bluetooth.target.wants
lrwxrwxrwx. 1 root root   41 Mar 14 19:18 dbus-org.bluez.service -> /usr/lib/systemd/system/bluetooth.service
lrwxrwxrwx. 1 root root   41 Mar 14 19:17 dbus-org.fedoraproject.FirewallD1.service -> /usr/lib/systemd/system/firewalld.service
lrwxrwxrwx. 1 root root   44 Mar 14 19:17 dbus-org.freedesktop.Avahi.service -> /usr/lib/systemd/system/avahi-daemon.service
lrwxrwxrwx. 1 root root   44 Mar 14 19:20 dbus-org.freedesktop.ModemManager1.service -> /usr/lib/systemd/system/ModemManager.service
lrwxrwxrwx. 1 root root   46 Mar 14 19:17 dbus-org.freedesktop.NetworkManager.service -> /usr/lib/systemd/system/NetworkManager.service
lrwxrwxrwx. 1 root root   57 Mar 14 19:17 dbus-org.freedesktop.nm-dispatcher.service -> /usr/lib/systemd/system/NetworkManager-dispatcher.service
lrwxrwxrwx. 1 root root   36 Mar 14 19:23 default.target -> /lib/systemd/system/graphical.target
drwxr-xr-x. 2 root root   85 Mar 14 19:16 default.target.wants
lrwxrwxrwx. 1 root root   35 Mar 14 19:19 display-manager.service -> /usr/lib/systemd/system/gdm.service
drwxr-xr-x. 2 root root   31 Mar 14 19:16 getty.target.wants
drwxr-xr-x. 2 root root 4096 Mar 14 19:26 graphical.target.wants
drwxr-xr-x. 2 root root 4096 Jun  6 12:44 multi-user.target.wants
drwxr-xr-x. 2 root root   29 Mar 14 19:17 nfs.target.wants
drwxr-xr-x. 2 root root   25 Mar 14 19:17 printer.target.wants
drwxr-xr-x. 2 root root 4096 Mar 14 19:17 sockets.target.wants
drwxr-xr-x. 2 root root   35 Mar 14 19:20 spice-vdagentd.target.wants
drwxr-xr-x. 2 root root 4096 Mar 14 19:17 sysinit.target.wants
drwxr-xr-x. 2 root root   83 Mar 14 19:16 system-update.target.wants
</pre>
 
For example if we take a look at the default httpd.service file, we'll see that by default target is set as the multiuser target:

<pre>
cat /usr/lib/systemd/system/httpd.service
[Unit]
Description=The Apache HTTP Server
After=network.target remote-fs.target nss-lookup.target

[Service]
Type=notify
EnvironmentFile=/etc/sysconfig/httpd
ExecStart=/usr/sbin/httpd $OPTIONS -DFOREGROUND
ExecReload=/usr/sbin/httpd $OPTIONS -k graceful
ExecStop=/bin/kill -WINCH ${MAINPID}
# We want systemd to give httpd some time to finish gracefully, but still want
# it to kill httpd after TimeoutStopSec if something went wrong during the
# graceful stop. Normally, Systemd sends SIGTERM signal right after the
# ExecStop, which would kill httpd. We are sending useless SIGCONT here to give
# httpd time to finish.
KillSignal=SIGCONT
PrivateTmp=true

[Install]
WantedBy=multi-user.target

</pre>

Hence the multi-user targets get's affected when you enable/disable the httpd service:

<pre>
$ systemctl enable httpd
ln -s '/usr/lib/systemd/system/httpd.service' '/etc/systemd/system/multi-user.target.wants/httpd.service'
$ systemctl disable httpd
rm '/etc/systemd/system/multi-user.target.wants/httpd.service'
</pre>

Now if we want to change this "WantedBy" directive to another target, e.g. the graphical.target, then we need to have a file that contains the following:

<pre>
$ cat /etc/systemd/system/httpd.service
[Install]
WantedBy=graphical.target
</pre>

In my case this file didn't exist, so I had to create it.  

Now when we enable/disable this service we now see:

<pre>
$ systemctl enable httpd
ln -s '/usr/lib/systemd/system/httpd.service' '/etc/systemd/system/multi-user.target.wants/httpd.service'
$ systemctl disable httpd
rm '/etc/systemd/system/multi-user.target.wants/httpd.service'
</pre> 

As you can see the directive settings specified in the <code>/etc/systemd/system</code> takes precedance over the corresponding settings in <code>/usr/lib/systemd/system</code>


In our case we only wanted to override just this one directive. But you can add more custom to your unit files to add/override other directives.