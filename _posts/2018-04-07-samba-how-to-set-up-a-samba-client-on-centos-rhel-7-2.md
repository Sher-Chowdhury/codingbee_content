---
ID: 3567
post_title: 'Samba &#8211; How to set up a Samba client on CentOS/RHEL 7'
author: sher
post_excerpt: "Here we'll cover how to configure a machine to access a Samba share."
layout: post
permalink: >
  https://codingbee.net/tutorials/rhce/samba-how-to-set-up-a-samba-client-on-centos-rhel-7-2
published: true
post_date: 2018-04-07 18:05:57
---
If you have directories on your machine that you want to share out to other machines then you can do this by <a href="http://codingbee.net/tutorials/rhce/nfs-set-up-an-nfs-server-on-centos-rhel-7">setting up your machine as an NFS server</a>. However with NFS you can only share out folders to machine that are in the same private network. If you want share folders to other machines over the public internet, then that's where you need to use the Samba/CIFS protocol.  You can follow along this article using this <a href="hhttps://github.com/Sher-Chowdhury/CentOS7-Samba-demo">vagrant project on Github</a>. 

We will walk through the following example:


<pre>
+--------------------------+              +--------------------------+
|                          |              |                          |
|      samba-storage       |              |       samba-client       |
|     (IP: 10.0.4.10)      |              |                          |
|                          |              |                          |
|                          |              |                          |
|                          |              |                          |
|  +------------------+    |              |     +---------------+    |
|  | /samba/export_rw |<----------------------->| /mnt/backups  |    |
|  +------------------+    |              |     +---------------+    |
|                          |              |                          |
|                          |              |                          |
+--------------------------+              +--------------------------+
</pre>



In this article we'll cover setting up the samba client side. We cover <a href="http://codingbee.net/tutorials/rhce/samba-set-up-cifs-server-on-centos-7">setting up a Samba server</a> in a separate article. First you need to install the samba software: 


<pre>
$ yum -y install samba samba-client cifs-utils
</pre>

Note: It's recommended to install the samba server software on all samba clients. 


Then we create our mount point:

<pre>
$ mkdir -p /mnt/backups
</pre>

Next we need check if we can connect to the samba server, and if we can to also check what shares are available:

<pre>
$ smbclient -L //samba-storage.local -U samba_user1
Enter SAMBA\samba_user1's password:
Domain=[SAMBA-STORAGE] OS=[Windows 6.1] Server=[Samba 4.6.2]

	Sharename       Type      Comment
	---------       ----      -------
	print$          Disk      Printer Drivers
	<strong>bckp_storage    Disk      Folder for storing backups</strong>
	IPC$            IPC       IPC Service (Samba server samba-storage)
	samba_user1     Disk      Home Directories
Domain=[SAMBA-STORAGE] OS=[Windows 6.1] Server=[Samba 4.6.2]

	Server               Comment
	---------            -------

	Workgroup            Master
	---------            -------
</pre>

Now we can test this by manually mounting this share like this:


<pre>
$ mount -t cifs -o user=samba_user1,password=password123 //samba-storage.local/export_rw /mnt/export/
</pre>

You can check if this command has worked using the df:

<pre>
$ df -h
Filesystem                          Size  Used Avail Use% Mounted on
/dev/mapper/centos-root              41G  1.2G   40G   3% /
devtmpfs                            486M     0  486M   0% /dev
tmpfs                               497M     0  497M   0% /dev/shm
tmpfs                               497M  6.7M  490M   2% /run
tmpfs                               497M     0  497M   0% /sys/fs/cgroup
/dev/sda1                          1014M  153M  862M  16% /boot
/dev/mapper/centos-home              20G   33M   20G   1% /home
vagrant                             466G   58G  409G  13% /vagrant
tmpfs                               100M     0  100M   0% /run/user/1000
tmpfs                               100M     0  100M   0% /run/user/0
<strong>//samba-storage.local/bckp_storage   41G  1.2G   40G   3% /mnt/backups</strong>
</pre> 

You can find out more about cifs mount option in the man pages:

<pre>$ man mount.cifs</pre>


To unmount this, do:

<pre>$ umount /mnt/backups </pre>

If you has, then you automount this samba share at boot time by adding the following entry to the <code>/etc/fstab</code> file:

<pre>
//samba-storage.local/bckp_storage /mnt/backups  cifs  username=samba_user1,password=password123,soft,rw  0 0
</pre>

To test this line, just run:


<pre>
$ mount -a
</pre>

Then check again if this has mounted successfully. If all is well try creating dummy content in the mountpoint and see if the content also shows up on the samba server. 


[post-content post_name=rhce-quiz] 

[accordion]
[toggle title="What rpms do you need to install?"]
$ yum -y install samba samba-client cifs-utils
[/toggle]
[toggle title="What is the command to manually test the samba access?"]
$ smbclient -L //samba-storage.local -U samba_user1
[/toggle]
[toggle title="What is the command to view more info about the samba mount options?"]
$ man mount.cifs
[/toggle]
[toggle title="What is the command to manually mount the samba share, //samba-storage.local/export_rw onto the mountpoint /mnt/export/, with username=samba_user1 and password=password123?"]
$ mount -t cifs -o user=samba_user1,password=password123 //samba-storage.local/export_rw /mnt/export/
[/toggle]
[toggle title="what entry needs to be added to the /etc/fstab file?"]
//samba-storage.local/bckp_storage /mnt/backups  cifs  username=samba_user1,password=password123,soft,rw,_netdev  0 0

# Notice how we specify the fqdn of the remote folder. We didn't use a colon. Also we didn't specify remote folder path. 
[/toggle]
[/accordion]