---
ID: 3087
post_title: 'Samba &#8211; How to set up a Samba client on CentOS/RHEL 7'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/samba-how-to-set-up-a-samba-client-on-centos-rhel-7
published: true
post_date: 2018-03-17 18:12:35
---
If you have directories on your machine that you want to share out to other machines then you can do this by <a href="http://codingbee.net/tutorials/rhce/nfs-set-up-an-nfs-server-on-centos-rhel-7">setting up your machine as an NFS server</a>. However with NFS you can only share out folders to machine that are in the same private network. If you want share folders to other machines over the public internet, then that's where you need to use the Samba/CIFS protocol.  You can follow along this article using this <a href="https://github.com/Sher-Chowdhury/NFS-CentOS7-demo">vagrant project on Github</a>. 

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



In this article we'll cover setting up the samba client side. We cover <a href="http://codingbee.net/tutorials/rhce/samba-set-up-cifs-server-on-centos-7">setting up a Samba server</a> in a separate article.  





First you need to install the samba software: 


<pre>
$ yum -y install samba samba-client cifs-utils
</pre>

Note: It's recommended to install the samba server software on all samba clients. 


Then we create our mount point:

<pre>
$ mkdir -p /mnt/backups
</pre>


Then we create a group that mirrors the same group that existing on the samba server:

<pre>
$ groupadd --gid 2000 sambagroup
</pre>


Now we need to add members to that group, in our case we'll add the root user:

<pre>
$ usermod -aG sambagroup root
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

You can check if this command has worked using the <strong>mount</strong> or <strong>df -h</strong> commands. 

To unmount this, do:

$ umount /mnt/backups 

If you has, then you automount this samba share at boot time by adding the following entry to the <code>/etc/fstab</code> file:

<pre>
//samba-storage.local/bckp_storage /mnt/backups  cifs  username=samba_user1,password=password123,soft,rw  0 0
</pre>

To test this line, just run:


<pre>
$ mount -a
</pre>

Then check again if this has mounted successfully using <strong>mount</strong> or <strong>df -h</strong> commands. If all is well try creating dummy content in the mountpoint and see if the content also shows up on the samba server.