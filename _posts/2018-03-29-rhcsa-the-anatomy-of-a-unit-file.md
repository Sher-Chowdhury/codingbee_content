---
ID: 452
post_title: 'Systemd &#8211; The anatomy of a unit file'
author: sher
post_excerpt: 'A <strong>unit</strong> is a resource that systemd can manage. If you look inside the /usr/lib/systemd/system directory, you will find files with different extensions, e.g. .socket, .target, .mount, ...etc.'
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-the-anatomy-of-a-unit-file
published: true
post_date: 2018-03-29 00:00:00
---
A <strong>unit</strong> is a resource that systemd can manage. There are different types of units:


<pre>
$ systemctl -t help
Available unit types:
service
socket
busname
target
snapshot
device
mount
automount
swap
timer
path
slice
scope   

</pre>

These units are represented in the form of files, referred 'unit files'. These files are located in <code>/usr/lib/systemd/system</code> directory. These files are named after the type of unit they represent.The structure of a unit file varies slightly from one unit file to another, but in general it looks something like this:

<pre>
cat /usr/lib/systemd/system/sshd.service
[Unit]
Description=OpenSSH server daemon
After=network.target sshd-keygen.service    # these units must be activated before this unit
Wants=sshd-keygen.service                 # systemd will attempt to start this at the same time as this unit. 
                                          # but can still function without it.  

[Service]
EnvironmentFile=/etc/sysconfig/sshd
ExecStart=/usr/sbin/sshd -D $OPTIONS
ExecReload=/bin/kill -HUP $MAINPID
KillMode=process
Restart=on-failure
RestartSec=42s

[Install]
WantedBy=multi-user.target
</pre>

The <strong><em>WantedBy</em></strong> directive is particularly important. That's because it tells systemctl which "target"  it should make responsible for starting up the resource during boot time (if this unit is enabled).   

You can find documentation about these directives here:

<pre>
$ man systemd.unit
</pre>

However you can view more details info about a particular unit type (e.g. service) like this:


<pre>
$ man systemd.service
</pre>


Note you can <a href="https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/sect-managing_services_with_systemd-unit_files#sect-Managing_Services_with_systemd-Unit_File_Modify">override a unit file</a>, by creating a unit file of the same name, and placing it in <code>/etc/systemd/system</code>.



<strong>Also see:</strong>

https://www.digitalocean.com/community/tutorials/understanding-systemd-units-and-unit-files


[post-content post_name=rhsca-quiz]

[accordion]
[toggle title="Which directory houses all the unit files?"]
/usr/lib/systemd/system
[/toggle]
[toggle title="Where can you find help info about units?"]
$ man systemd.unit
[/toggle]
[toggle title="Where can you find help info about the service unit type?"]
$ man systemd.service
[/toggle]
[toggle title="How do you override some settings in the postfix.server unit file?"]
$ cp /usr/lib/systemd/system/postfix.service /etc/systemd/system/postfix.service
# then use vim to customise the copied file accordingly. 
[/toggle]
[/accordion]