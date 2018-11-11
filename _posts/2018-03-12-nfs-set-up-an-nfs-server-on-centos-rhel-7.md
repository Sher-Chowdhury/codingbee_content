---
ID: 458
post_title: 'NFS &#8211; Set up an NFS server on CentOS/RHEL 7'
author: sher
post_excerpt: >
  If you have directories on your machine
  that you want to share out to other
  machines then you can do this by setting
  up your system as an NFS server.
layout: post
permalink: >
  https://codingbee.net/tutorials/rhce/nfs-set-up-an-nfs-server-on-centos-rhel-7
published: true
post_date: 2018-03-12 00:00:00
---
If you have directories on your machine that you want to share out to other machines then you can do this by setting up your system as an NFS server. You can follow along this article using this <a href="https://github.com/Sher-Chowdhury/NFS-CentOS7-demo">vagrant project on Github</a>. 

We will walk through the following example:


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



To do this, you first need to ensure the following package is installed:

<pre>
$ yum install nfs-utils
</pre> 

If SELinux is running, the we need to ensure SE booleans setting are enabled:

<pre>
setsebool -P nfs_export_all_rw 1
setsebool -P nfs_export_all_ro 1
</pre>

Here we made (P)ersistant changes, you can check with getsebool command to confirm that the changes have been made. 

Next if you want firewalld running then you need to add the following services to the whitelist:


<pre>
$ firewall-cmd --permanent --add-service=nfs
$ firewall-cmd --permanent --add-service=mountd
$ firewall-cmd --permanent --add-service=rpc-bind
$ systemctl restart firewalld
</pre>

Next we make the mount points as per our example:



<pre>
$ mkdir -p /nfs/export_ro
$ mkdir -p /nfs/export_rw
</pre>

Also change ownership of these folders to:

<pre>
$ chown nfsnobody:nobody /nfs/export_rw
$ chown nfsnobody:nobody /nfs/export_rw
</pre>

The nfsnobody is a special reserved name that's used for this purpose. Now we have:

<pre>
[root@nfs-storage ~]# ll -Z /nfs
drwxr-xr-x. nfsnobody  nobody    unconfined_u:object_r:default_t:s0 export_ro
drwxr-xr-x. nfsnobody  nobody    unconfined_u:object_r:default_t:s0 export_rw
</pre>


The SELinux type attribute needs to be fixed, which we do by running:

<pre>
[root@nfs-storage ~]# semanage fcontext -a -t public_content_rw_t  "/nfs(/.*)?"
[root@nfs-storage ~]# restorecon -Rv /nfs

[root@nfs-storage ~]# ll -Z /nfs/
drwxr-xr-x. nfsnobody  nobody    unconfined_u:object_r:public_content_rw_t:s0 export_ro
drwxr-xr-x. nfsnobody  nobody    unconfined_u:object_r:public_content_rw_t:s0 export_rw
</pre>

Tip: if you forget what the tags are called, run the following for a remindoer:

<pre>
$ seinfo -t | grep public
</pre>



The security attribute 'public_content_ro_t' doesn't appear to exist anymore. Instead it look like this has been renamged to simply public_content_t. For more info, see:

<pre>
$ man nfsd_selinux
</pre>

Next we need to create the following nfs config file, with the content:

<pre>
$ cat /etc/exports
/nfs/export_ro  *(sync)
/nfs/export_rw  *(rw,no_root_squash)
</pre>

Note this file might not exist, or does exist but is empty. The 'sync' option is basically to make it read only. For more info, check out:

<pre>
$ man exports
</pre>

This man page also gives sample entries at the bottom. 


Now start and enable the nfs-server service:

<pre>
$ systemctl enable nfs-server
$ systemctl restart nfs-server
</pre>

Now we are ready to test this by <a href="http://codingbee.net/tutorials/rhcsa/nfs-how-to-set-up-an-nfs-client-on-centos-7">Setting up an NFS client on CentOS 7</a>. 

What command can you run to check if the exports have been successfully exported:


<pre>
[root@nfs-storage nfs]# exportfs -avr
exporting *:/nfs/export_rw
exporting *:/nfs/export_ro
</pre>

[post-content post_name=rhsca-quiz] 

In this quiz we'll assume the 2 folders you want to export are called /nfs/export_ro and /nfs/export_rw

[accordion]
[toggle title="What rpm do you need to install to setup an NFS server?"]
$ yum install nfs-utils
[/toggle]
[toggle title="What SELinux boolean setting do you need to apply?"]
$ setsebool -P nfs_export_all_rw 1
$ setsebool -P nfs_export_all_ro 1
[/toggle]
[toggle title="What firewall rules you do you need allow?"]
$ firewall-cmd --permanent --add-service=nfs
$ firewall-cmd --permanent --add-service=mountd
$ firewall-cmd --permanent --add-service=rpc-bind
$ systemctl restart firewalld
[/toggle]
[toggle title="What folders do you need to create and what permissions should they have?"]
$ mkdir -p /nfs/export_ro
$ mkdir -p /nfs/export_rw
$ chown nfsnobody:nobody /nfs/export_rw
$ chown nfsnobody:nobody /nfs/export_ro
[/toggle]
[toggle title="What is the command to help identify what selinux fcontext tags to use"]
$ seinfo -t | grep public
[/toggle]
[toggle title="What SELinux rules should be applied to these folders?"]
$ semanage fcontext -a -t public_content_t  "/nfs/export_ro(/.*)?"
$ semanage fcontext -a -t public_content_rw_t  "/nfs/export_rw(/.*)?"
$ restorecon -Rv /nfs
[/toggle]
[toggle title="What config file do you need to create, and what should it's content be (also restrict export_rw access so that only box with ip address of 10.0.6.11 can access it)?"]
$ cat /etc/exports
/nfs/export_ro  *(sync)
/nfs/export_rw  10.0.6.11(rw,no_root_squash)
[/toggle]
[toggle title="Where can you find more info about this config file?"]
$ man exports
[/toggle]
[toggle title="What are the commands to enable+start the nfs deamon?"]
$ systemctl enable nfs-server
$ systemctl restart nfs-server
[/toggle]
[toggle title="What can you check to confirm the nfs exports are now available?"]
view the etab file: /var/lib/nfs/etab
also you can run:
$ exportfs -avr
[/toggle]
[toggle title="What is the man page to view more info on nfs related selinux stuff?"]
$ man nfsd_selinux
[/toggle]
[/accordion]