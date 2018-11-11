---
ID: 3016
post_title: 'NFS &#8211; How to set up an NFS client on CentOS/RHEL 7'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/nfs-how-to-set-up-an-nfs-client-on-centos-7
published: true
post_date: 2018-03-12 13:53:25
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

We can nfs mounts persistant by adding an entry to the <code>/etc/fstab</code> file:


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

[accordion]
[toggle title="01. What is the command to install the nfs client side packages?"]
$ yum install nfs-utils
[/toggle]
[toggle title="02. What is the command to check what folders the server 'The-Server' is sharing via nfs?"]
$ showmount -e The-Server
[/toggle]
[toggle title="03. What is the command to install the CIFS/Samba client side packages?"]
$ yum install samba-client cifs-utils
[/toggle]
[toggle title="04. What is the command to check what folders the server 'The-Server' is sharing via CIFS/Samba by querying as the user 'vagrant'?"]
$ smbclient -L The-Server <span style="letter-spacing:0.1px">-</span>-user vagrant
[/toggle]
[toggle title="05. What is the command to create logical folder structure for an NFS mountpoint?"]
$ mkdir -p /nfs/{server-name}/{mountpoint}
[/toggle]
[toggle title="06. What is the command to manually mount onto the mountpoint, '/nfs/The-Server/Shared-Folder' the nfs share, '/nfs/Shared-Folder' that has been made available by the server 'The-Server'?"]
$ mount -t nfs The-Server:/nfs/Shared-Folder /nfs/The-Server/Shared-Folder
[/toggle]
[toggle title="07. What is the command to create logical folder structure for an CIFS mountpoint, assuming the nfs server is called 'The-Server' and the folder being shared out is called 'Shared-Folder'?"]
$ mkdir -p /cifs/The-Server/Shared-Folder
[/toggle]
[toggle title="08. What is the command to manually mount onto the mountpoint, '/cifs/The-Server/Shared-Folder' the cifs share, 'Shared-Folder' that has been made available by the server 'The-Server', where the smb username is 'vagrant', and corresponding password is 'admin'?"]
$ mount -t cifs -o user=vagrant,password=admin <mark>//</mark>The-Server/Shared-Folder /cifs/The-Server/Shared-Folder
# Note, you must use the double-slash syntax. 
[/toggle]
[toggle title="09. What two commands can you run to check the cifs or nfs mount has worked?"]
$ mount
# or 
$ df -h
[/toggle]
[/accordion]