---
ID: 412
post_title: 'RHCSA &#8211; Automounting using systemd and autofs'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-automounting-using-systemd-and-autofs
published: true
post_date: 2015-06-08 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

Let's say we have the following scenario:
<ul>
	<li>we have the partition: <strong>/dev/sdb1</strong></li>
	<li>we have the mountpoint:  <strong>/tmp/sdb1custom</strong></li>
	<li>the /dev/sdb1 partition has the xfs mountpoint installed on it.</li>
	<li>The /dev/sdb1 needs to be automounted by the <strong>multi-user.target</strong></li>
</ul>


[accordion]
[toggle title="What will be compatible name of the mount unit file that you need to create?"]
$ touch /etc/systemd/system/tmp-sdb1custom.mount
# replace the "/" to create the mount unit's file name.
[/toggle]
[toggle title="What should this file contain?"]
$ cat /etc/systemd/system/tmp-sdb1custom.mount
[Unit]
Description=My new file system

[Mount]
What=/dev/sdb1
Where=/tmp/sdb1custom
Type=xfs
Options=grpquota,ro

[Install]
WantedBy=multi-user.target
[/toggle]
[toggle title="What is the command to activate the mount?"]
$ systemctl start tmp-sdb1custom.mount
[/toggle]
[toggle title="What is the command to activate the automounting?"]
$ systemctl enable tmp-sdb1custom.mount
[/toggle]
[toggle title="So far we have created a normal systemd mount, what is the next thing you need to to set up autofs style automounting?"]
# stop + disable our new tmp-sdb1custom.mount:
$ systemctl stop tmp-sdb1custom.mount
$ systemctl disable tmp-sdb1custom.mount
[/toggle]
[toggle title="What do you do next?"]
# create the tmp-sdb1custom.mount file
$ cp /etc/systemd/system/tmp-sdb1custom.mount /etc/systemd/system/tmp-sdb1custom.automount
[/toggle]
[toggle title="What do you do next?"]
# modify this file so it looks like this, i.e.  has the '[automount]' stanza


$ cat /etc/systemd/system/tmp-sdb1custom.automount
[Unit]
Description=My new automounted file system

[Automount]

[Install]
WantedBy=multi-user.target

[/toggle]
[toggle title="What do you do next?"]
# start and enable the new automount unit
$ systemctl start tmp-sdb1custom.automount
$ systemctl enable tmp-sdb1custom.automount
[/toggle]

[/accordion]

<hr/>



Earlier when we covered <a href="http://codingbee.net/tutorials/rhcsa/rhcsa-autofs/">autofs</a>, we found how a filesystem is not mounted until you actually cd into it's mount point.  

You can achieve the same thing with systemd as well. To get started, you first need to create a mountpoint and a *.mount file, like we did in the previous lesson, so that it appears that we are going to <a href="http://codingbee.net/tutorials/rhcsa/rhcsa-auto-mount-filesystems-during-boot-time-using-systemd/">auto-mount the file system during machine boot</a>. But this time we don't start the mount "unit" and we ensure that it is disabled.


Let's use the filesystem, sdb1, from the last lesson as an example. So we have our filesystem:


<pre>$ ls -l /dev/sdb1
brw-rw----. 1 root disk 8, 17 Jun  9 06:12 /dev/sdb1
</pre>

...and we have our mountpoint:


<pre>$ ls -l /tmp | grep sdb1
drwxr-xr-x. 2 root root  6 Jun  8 21:01 sdb1custom
</pre>

...and we have our *.mount file:


<pre>
$ cat /etc/systemd/system/tmp-sdb1custom.mount
[Unit]
Description=My new file system

[Mount]
What=/dev/sdb1
Where=/tmp/sdb1custom
Type=xfs
Options=grpquota,ro

[Install]
WantedBy=multi-user.target
</pre>

Next in order to take the automount route, we actually need to stop+disable this unit: 


<pre>
$ systemctl stop tmp-sdb1custom.mount
$ systemctl disable tmp-sdb1custom.mount
rm '/etc/systemd/system/multi-user.target.wants/tmp-sdb1custom.mount'
</pre>



So far so good. Now we come to the steps we need to take to set up the actual automounting feature. To do this we create a new <strong>*.automount</strong> unit file in the /etc/systemd/system directory. This file must have the same name as the corresponding *.mount file, but with a ".automount" suffix. Therefore it's easier to create this file by making it a copy of the .mount file:

<pre>
$ cp /etc/systemd/system/tmp-sdb1custom.mount /etc/systemd/system/tmp-sdb1custom.automount
$ ls -l /etc/systemd/system/tmp-sdb1custom.automount
-rw-r--r--. 1 root root 151 Jun  9 06:30 /etc/systemd/system/tmp-sdb1custom.automount
</pre>

Now you need to edit this file so that it looks like this:

<pre>
$ cat /etc/systemd/system/tmp-sdb1custom.automount
[Unit]
<del datetime="2015-11-07T21:11:09+00:00">Description=My new file system</del>
Description=My new automounted file system

<del datetime="2015-11-07T21:11:09+00:00">
[Mount]
What=/dev/sdb1
Where=/tmp/sdb1custom
Type=xfs
Options=grpquota,ro
</del>

[Automount]
Where=/tmp/sdb1custom

[Install]
WantedBy=multi-user.target
</pre>

Notice, that this unit's main config section is now called "[Automount]". Also notice that I included a "Where" setting in this section. This is actually optional, and if specified, it must match what is in the corresponding .mount file. It's doesn't work like some kind of over-ride mechanism, as you might have initially suspected, you will get an error message if you tried.  

Now let's enable and start this unit:


<pre>
$ systemctl status tmp-sdb1custom.automount
tmp-sdb1custom.automount - My new automounted file system
   Loaded: loaded (/etc/systemd/system/tmp-sdb1custom.automount; disabled)
   Active: inactive (dead)
    Where: /tmp/sdb1custom

$ systemctl start tmp-sdb1custom.automount
$ systemctl enable tmp-sdb1custom.automount
ln -s '/etc/systemd/system/tmp-sdb1custom.automount' '/etc/systemd/system/multi-user.target.wants/tmp-sdb1custom.automount'
$ systemctl status tmp-sdb1custom.automount
tmp-sdb1custom.automount - My new automounted file system
   Loaded: loaded (/etc/systemd/system/tmp-sdb1custom.automount; <mark>enabled</mark>)
   Active: <mark>active (waiting)</mark> since Tue 2015-06-09 06:48:00 BST; 11s ago
    Where: /tmp/sdb1custom

Jun 09 06:48:00 localhost.localdomain systemd[1]: Starting My new automounted file system.
Jun 09 06:48:00 localhost.localdomain systemd[1]: Set up automount My new automounted file system.

</pre>
   
Once it has been started, you will see that the mount command shows that it is now a auto-mounting (aka autofs) mountpoint:

<pre>
$ mount | grep sdb1
systemd-1 on /tmp/sdb1custom type autofs (rw,relatime,fd=45,pgrp=1,timeout=300,minproto=5,maxproto=5,direct)
</pre>



However lsblk shows that it hasn't been mounted yet:


<pre>
$ lsblk
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   20G  0 disk
├─sda1            8:1    0  500M  0 part /boot
└─sda2            8:2    0 19.5G  0 part
  ├─centos-swap 253:0    0    2G  0 lvm  [SWAP]
  └─centos-root 253:1    0 17.5G  0 lvm  /
sdb               8:16   0    2G  0 disk
└─sdb1            8:17   0    1G  0 part
sr0              11:0    1 1024M  0 rom
</pre>

Let's now cd into the mountpoint and try again:


<pre>
$ cd /tmp/sdb1custom/
</pre>

Then try lsblk again:


<pre>
$ lsblk
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   20G  0 disk
├─sda1            8:1    0  500M  0 part /boot
└─sda2            8:2    0 19.5G  0 part
  ├─centos-swap 253:0    0    2G  0 lvm  [SWAP]
  └─centos-root 253:1    0 17.5G  0 lvm  /
sdb               8:16   0    2G  0 disk
└─sdb1            8:17   0    1G  0 part <mark>/tmp/sdb1custom</mark>
sr0              11:0    1 1024M  0 rom

</pre>


Success!


Also the mount command also shows that it has been mounted:


<pre>
$ mount | grep sdb1
systemd-1 on /tmp/sdb1custom type autofs (rw,relatime,fd=45,pgrp=1,timeout=300,minproto=5,maxproto=5,direct)
<strong>/dev/sdb1 on /tmp/sdb1custom type xfs (ro,relatime,seclabel,attr2,inode64,grpquota)
</strong>
</pre>