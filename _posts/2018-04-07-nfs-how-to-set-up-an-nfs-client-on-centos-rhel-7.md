---
ID: 3556
post_title: 'NFS &#8211; How to set up an NFS client on CentOS/RHEL 7'
author: sher
post_excerpt: "Network Files System (NFS) is a protocol that let's one Linux box (NFS server) to share a folder with another Linux box (NFS Client). On the NFS client this shared folder looks like just an ordinary folder. NFS only works in an internal network so you can share folders over the public internet."
layout: post
permalink: >
  https://codingbee.net/tutorials/rhce/nfs-how-to-set-up-an-nfs-client-on-centos-rhel-7
published: true
post_date: 2018-04-07 13:54:00
---
Network Files System (NFS) is a protocol that let's one Linux box (NFS server) to share a folder with another Linux box (NFS Client). On the NFS client this shared folder looks like just an ordinary folder. NFS only works in an internal network so you can share folders over the public internet.

This article doesn't cover how to <a href="http://codingbee.net/tutorials/rhce/rhce-set-up-an-nfs-server/">setup an NFS server</a>, instead we will assume that we already have an NFS server already setup and we want to configure an NFS client to connect to it. We created a <a href="https://github.com/Sher-Chowdhury/NFS-CentOS7-demo">NFS vagrant project on github</a> to help you following along with this example. In our example we have:

<pre>
+--------------------------+              +--------------------------+
|                          |              |                          |
|       nfs-storage        |              |        nfs-client        |
|     (IP: 10.0.6.10)      |              |                          |
|                          |              |                          |
|                          |              |                          |
|                          |              |                          |
|                          |              |                          |
|   +-----------------+    |              |     +---------------+    |
|   | /nfs/export_ro  |<----------------------->| /mnt/ref_data |    |
|   +-----------------+    |              |     +---------------+    |
|                          |              |                          |
|   +-----------------+    |              |     +---------------+    |
|   | /nfs/export_rw  |<----------------------->| /mnt/backups  |    |
|   +-----------------+    |              |     +---------------+    |
|                          |              |                          |
|                          |              |                          |
+--------------------------+              +--------------------------+

</pre>


With the vagrant project, the nfs server (nfs-storage) will already be set up for you. So you just need to make configure the nfs client to access the 2 NFS folders made available by nfs-storage. 


To start with, on NFS client you install:

<pre>
[root@nfs-client mnt]# yum install nfs-utils
</pre>


Next see if you your client can query for available exports:

<pre>
[root@nfs-client mnt]# showmount -e 10.0.6.10
Export list for 10.0.6.10:
/nfs/export_rw *
/nfs/export_ro *
</pre>

Next we'll try to mount these, first we need to create mount-points for these exports:


<pre>
$ mkdir -p /mnt/backups
$ mkdir -p /mnt/ref_data
</pre>


Now we can mount them (non-persistently) using the mount command:


<pre>
[root@nfs-client ~]# mount -t nfs 10.0.6.10:/nfs/export_ro /mnt/ref_data
[root@nfs-client ~]# mount -t nfs 10.0.6.10:/nfs/export_rw /mnt/backups
</pre>

You can confirm this has worked by running either of the following commands:

<pre>
$ mount 
# or
$ df -h
</pre>


Now you can try creating content inside the mount-points and will. 


<h4>Automount NFS folders</h4>

We can NFS mounts persistant by adding an entry to the <code>/etc/fstab</code> file:


<pre>
10.0.6.10:/nfs/export_ro   /mnt/ref_data   nfs   soft,timeo=100,_netdev,ro   0   0
10.0.6.10:/nfs/export_rw   /mnt/backups    nfs   soft,timeo=100,_netdev,rw   0   0
</pre>

Note you can find more option about nfs mount options here:

<pre>
$ man nfs

# also see the following for more general mount options:

$ man mount
</pre>

Here we used 'soft' to make the mounting to error out rather than hanging (although this can cause data corruption). Also we set (timeo)ut to 100 deciseconds (10 seconds). Now we test this by running:

<pre>
$ mount -a
</pre>

you can then do <code>mount</code> or <code>df -h</code>. 


Note: you can also automount, such as using a tool called autofs, or creating a custom systemd target. Also might be able to use a tool called sshfs too. 


<h4>Try the RHCSA quiz</h4>
By the end of this article you should be able to answer the following questions:

In this quiz we'll assume that we have created 2 mount points /mnt/backups and /mnt/ref_data. Also our NFS server's ip address is 10.0.6.10. 

[accordion]
[toggle title="What is the command to install the nfs client side packages?"]
$ yum install nfs-utils
[/toggle]
[toggle title="What is the command to check what folders our NFS server has made available?"]
$ showmount -e 10.0.6.10
[/toggle]
[toggle title="What is the command to manually mount these folders?"]
$ mount -t nfs 10.0.6.10:/nfs/export_ro /mnt/ref_data
$ mount -t nfs 10.0.6.10:/nfs/export_rw /mnt/backups
[/toggle]
[toggle title="What command can you run to check mount command has worked?"]
$ mount
# or 
$ df -h
# note: lsblk wont list these since they lsblk only lists locally attached devices. 
[/toggle]
[toggle title="What entries do we need to add to the fstab file to make these 2 mounts persistant?"]
10.0.6.10:/nfs/export_ro   /mnt/ref_data   nfs   soft,timeo=100,_netdev,ro   0   0
10.0.6.10:/nfs/export_rw   /mnt/backups    nfs   soft,timeo=100,_netdev,rw   0   0
[/toggle]
[/accordion]