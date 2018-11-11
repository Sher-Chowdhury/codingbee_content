---
ID: 411
post_title: 'RHCSA &#8211; Mount Filesystems during boot time using Systemd'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-mount-filesystems-during-boot-time-using-systemd
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

[/accordion]

<hr/>



In an earlier lesson we saw how to use the <a href="http://codingbee.net/tutorials/rhcsa/rhcsa-mounting-a-partition/">/etc/fstab</a> file to automatically mount a file system during boot time.  However there's a new way to automount using systemd. This is done by creating a "mount" unit file. You should find some existing ones here:


<pre>
$ ls -l /usr/lib/systemd/system | grep "mount$"
-rw-r--r--. 1 root root  636 Jun 10  2014 dev-hugepages.mount
-rw-r--r--. 1 root root  590 Jun 10  2014 dev-mqueue.mount
-rw-r--r--. 1 root root  115 Jun 10  2014 proc-fs-nfsd.mount
-rw-r--r--. 1 root root  693 Jun 10  2014 proc-sys-fs-binfmt_misc.automount
-rw-r--r--. 1 root root  603 Jun 10  2014 proc-sys-fs-binfmt_misc.mount
-rw-r--r--. 1 root root  681 Jun 10  2014 sys-fs-fuse-connections.mount
-rw-r--r--. 1 root root  685 Jun 10  2014 sys-kernel-config.mount
-rw-r--r--. 1 root root  628 Jun 10  2014 sys-kernel-debug.mount
-rw-r--r--. 1 root root  669 Jun 10  2014 tmp.mount
-rw-r--r--. 1 root root  131 Jun 10  2014 var-lib-nfs-rpc_pipefs.mount
</pre>  
 
To see which of these are active, do:


<pre>
$ systemctl list-units --type=mount
UNIT                         LOAD   ACTIVE SUB     DESCRIPTION
-.mount                      loaded active mounted /
boot.mount                   loaded active mounted /boot
dev-hugepages.mount          loaded active mounted Huge Pages File System
dev-mqueue.mount             loaded active mounted POSIX Message Queue File System
proc-fs-nfsd.mount           loaded active mounted RPC Pipe File System
sys-kernel-config.mount      loaded active mounted Configuration File System
sys-kernel-debug.mount       loaded active mounted Debug File System
var-lib-nfs-rpc_pipefs.mount loaded active mounted RPC Pipe File System

LOAD   = Reflects whether the unit definition was properly loaded.
ACTIVE = The high-level unit activation state, i.e. generalization of SUB.
SUB    = The low-level unit activation state, values depend on unit type.

8 loaded units listed. Pass --all to see loaded but inactive units, too.
To show all installed unit files use 'systemctl list-unit-files'.
</pre>

Now, let's say I created a new partition, sdb1, and I have a filesytem installed on it. 


<pre>
$ lsblk
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   20G  0 disk
├─sda1            8:1    0  500M  0 part /boot
└─sda2            8:2    0 19.5G  0 part
  ├─centos-swap 253:0    0    2G  0 lvm  [SWAP]
  └─centos-root 253:1    0 17.5G  0 lvm  /
sdb               8:16   0    2G  0 disk
<strong>└─sdb1            8:17   0    1G  0 part</strong>
sr0              11:0    1 1024M  0 rom
</pre>


I now want to automount sdb1 using systemd. The first thing I want to do is create the mountpoint, and for this example, I'll create:


<pre>$ mkdir /tmp/sdb1custom</pre>



Next I need to create a .mount file. I can't create the .mount file in /usr/lib/systemd/system, because that directory is for systemd's internal use only. Instead I create it in <code>/etc/systemd/system/</code>. The easiest/quickest way to do this is by making a copy from an existing *.mount file, in my case I'll use tmp.mount as my template:

<pre>
$ cp /usr/lib/systemd/system/tmp.mount /etc/systemd/system/tmp-sdb1custom.mount</pre>

Note: the .mount file's name has to mirror the mountpoint's name, just replace the slashes with hyphens.  
 
Now, let's view it's content:

<pre>
$ cat /etc/systemd/system/tmp-sdb1custom.mount
#  This file is part of systemd.
#
#  systemd is free software; you can redistribute it and/or modify it
#  under the terms of the GNU Lesser General Public License as published by
#  the Free Software Foundation; either version 2.1 of the License, or
#  (at your option) any later version.

<strong>[Unit]
Description=Temporary directory
</strong>Documentation=man:hier(7)
Documentation=http://www.freedesktop.org/wiki/Software/systemd/APIFileSystems
DefaultDependencies=no
Conflicts=umount.target
Before=local-fs.target umount.target

<strong>[Mount]
What=tmpfs
Where=/tmp
Type=tmpfs
Options=mode=1777,strictatime
</strong>


# Make 'systemctl enable tmp.mount' work:
<strong>[Install]
WantedBy=local-fs.target
</strong>

</pre>


I have highlighted the mandatory parts in bold above. Using this template as a starting point, I created the following file:

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

Notice that when compared to a *.service file, a *.mount file has a "[Mount]" section instead of a "[Service]" section. 


Now we can test this by manually mounting using the sytemctl command. But let's confirm it isn't mounted yet:


<pre>
$ mount | grep sdb1
</pre> 

Now let's mount it using the systemctl command:

<pre>
$ systemctl start tmp-sdb1custom.mount
</pre>

Notice that's it's similar to the command you use to start a service. We can confirm that the mount has been successful using systemctl:

<pre>$ systemctl status tmp-sdb1custom.mount
tmp-sdb1custom.mount - My new file system
   Loaded: loaded (/etc/systemd/system/tmp-sdb1custom.mount; disabled)
   Active: <strong>active (mounted)</strong> since Mon 2015-06-08 23:09:21 BST; 6s ago
    Where: /tmp/sdb1custom
     What: /dev/sdb1
  Process: 1745 ExecMount=/bin/mount /dev/sdb1 /tmp/sdb1custom -t xfs -o grpquota,ro (code=exited, status=0/SUCCESS)

Jun 08 23:09:21 localhost.localdomain systemd[1]: Mounting My new file system...
</pre>


We can also check it worked using the mount command:

<pre>
$ mount | grep sdb1
/dev/sdb1 on /tmp/sdb1custom type xfs (ro,relatime,seclabel,attr2,inode64,grpquota)
</pre>


Another way to check is using lsblk:


<pre>$ lsblk
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   20G  0 disk
├─sda1            8:1    0  500M  0 part /boot
└─sda2            8:2    0 19.5G  0 part
  ├─centos-swap 253:0    0    2G  0 lvm  [SWAP]
  └─centos-root 253:1    0 17.5G  0 lvm  /
sdb               8:16   0    2G  0 disk
<strong>└─sdb1            8:17   0    1G  0 part /tmp/sdb1custom</strong>
sr0              11:0    1 1024M  0 rom
</pre>

Now to unmount we do:


<pre>$ umount /dev/sdb1</pre>









Now if we want this filesystem to auto-mount during the boot process, we need to enable it (in the same way that we do for services), therefore we do:


<pre>
$ systemctl enable tmp-sdb1custom.mount
ln -s '/etc/systemd/system/tmp-sdb1custom.mount' '/etc/systemd/system/multi-user.target.wants/tmp-sdb1custom.mount'
</pre>

Now let's confirm that it is enabled:


<pre>
$ systemctl status tmp-sdb1custom.mount
tmp-sdb1custom.mount - My new file system
   Loaded: loaded (/etc/systemd/system/tmp-sdb1custom.mount; <mark>enabled</mark>)
   Active: active (mounted) since Mon 2015-06-08 23:09:21 BST; 6min ago
    Where: /tmp/sdb1custom
     What: /dev/sdb1

Jun 08 23:09:21 localhost.localdomain systemd[1]: Mounting My new file system...
</pre>



Now let's go ahead and reboot the machine. After that let's see if sdb1 get's automounted:


<pre>
$ mount | grep sdb1
/dev/sdb1 on /tmp/sdb1custom type xfs (ro,relatime,seclabel,attr2,inode64,grpquota)
</pre>

Success!