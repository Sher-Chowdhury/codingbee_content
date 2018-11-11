---
ID: 3057
post_title: 'NFS &#8211; Use Kerberos to control NFS access on CentOS/RHEL 7'
author: sher
post_excerpt: "The NFS setups we've covered so far didn't have any authentication or encryption setup. To some extent that wasn't needed since NFS only works inside internal networks. However it is possible to setup authentication+encryption using Kerberos."
layout: post
permalink: >
  https://codingbee.net/tutorials/rhce/nfs-use-kerberos-to-control-nfs-access-on-centos-7
published: true
post_date: 2018-03-13 22:47:52
---
The NFS setups we've covered so far didn't have any authentication or encryption setup. To some extent that wasn't needed since NFS only works inside internal networks. However it is possible to setup authentication+encryption using Kerberos. Here's an example setup that we'll be working through:


<pre>
                   +------------------------------+
                   |                              |
                   |        Kerberos-Server       |
                   |          kdc.cb.net          |
                   |        (IP: 10.0.9.11)       |
                   |                              |
                   |                              |
                   |                              |
                   |                              |
                   +------------------------------+
                       ^                       ^
                       |                       |
                       |                       |
                       |                       |
                       v                       v
+----------------------------+              +-----------------------------+
|                            |              |                             |
|       nfs-storage          |              |         nfs-client          |
|    nfs-storage.cb.net      |              |      nfs-client.cb.net      |    
|     (IP: 10.0.9.12)        |              |       (IP: 10.0.9.13)       |
|                            |              |                             |
|                            |              |                             |
|                            |              |                             |
|   +-----------------+      |   kerberos   |     +---------------+       |
|   | /nfs/export_rw  |<---  ---------------|---->| /mnt/backups  |       |
|   +-----------------+      | auth+encrypt |     +---------------+       |
|                            |              |                             |
|                            |              |                             |
+----------------------------+              +-----------------------------+
</pre>

You can follow along this example using our <a href="https://github.com/Sher-Chowdhury/CentOS7-kerberos-nfs-demo">CentOS 7 NFS-Kerberos vagrant project</a>.  


here, we have one kerberos server (Kerberos-Server) and 2 kerberos clients (nfs-storage and nfs-client). The nfs-storage is also going to be our NFS server and nfs-client is going to an nfs client. So to set up this example, we need to do some preliminary tasks. 

<ol>
	<li><a href="https://codingbee.net/tutorials/rhce/rhce-kerberos#setup-kerberos-server">Setup Kerberos-Server to be our Kerberos Server</a> - However we don't need to do any ssh related changes or create the krbtest user.</li>
	<li><a href="https://codingbee.net/tutorials/rhce/rhce-kerberos#Kerberos-Client-Setup">Setup nfs-storage and nfs-client to both be Kerberos Clients</a> - again, we don't need to do any ssh stuff or create krbtest user</li>
	<li><a href="http://codingbee.net/tutorials/rhce/nfs-set-up-an-nfs-server-on-centos-rhel-7">Setup nfs-storage to be our NFS server</a> - We'll just create one folder /nfs/export_rw</li>
	<li><a href="http://codingbee.net/tutorials/rhcsa/nfs-how-to-set-up-an-nfs-client-on-centos-7">Setup up nfs-client to be our nfs client</a></li>
</ol>


Once all the above is done a few more extra steps are needed to setup the NFS+Kerberos integration on the Server and Client. 

<h2>Setup Kerberos+NFS integration on the NFS Server</h2>

On the NFS server, we need to register the NFS service as a principal on Kerberos, using the kadmin command:

<pre>
$ kadmin
</pre>

We enter the root kerberos password when prompted, then within the kerberos interactive shell, we run the following commands:


<pre>
addprinc -randkey nfs/nfs-storage.cb.net
ktadd nfs/nfs-storage.cb.net
quit
</pre> 

Next we add the kerberos setting in the <code>/etc/exports</code> file,

<pre>
$ cat /etc/exports
/nfs/export_rw  *(rw,no_root_squash<strong>,sec=krb5</strong>)
</pre>

Next you need to apply the new settings, by running the following 2 commands:

<pre>
$ systemctl restart nfs-server
</pre>

You can check whether krb-nfs encryption has been enabled by going to:

<pre>
cat /var/lib/nfs/etab
/nfs/export_rw	*(rw,sync,wdelay,hide,nocrossmnt,secure,no_root_squash,no_all_squash,no_subtree_check,secure_locks,acl,no_pnfs,anonuid=65534,anongid=65534,<strong>sec=krb5</strong>,secure,no_root_squash,no_all_squash)
</pre>



<h2>Setup Kerberos+NFS integration on the NFS Client</h2>

On the client side, you need to register the NFS service as a principal on Kerberos, using the kadmin command (pretty much the same thing as done on the nfs server):

<pre>
$ kadmin -p root/admin@CB.NET
</pre>

Note, we explicitly specified the root principle. That's because for some reason it kept trying to use 'host/admin@CB.NET'. 

We enter the root kerberos password when prompted, then within the kerberos interactive shell, we run the following commands:


<pre>
addprinc -randkey nfs/nfs-client.cb.net
ktadd nfs/nfs-client.cb.net
quit
</pre> 

Next you need to activate the following <strong>target</strong>:

<pre>
$ systemctl enable nfs-client.target
$ systemctl restart nfs-client.target
</pre>

Next you are ready to attempt the mounting, first you can test the mounting non-persistantly by running:

<pre>
$ mount -t nfs -o <strong>sec=krb5</strong>,rw nfs-storage.cb.net:/nfs/export_rw /mnt/backups 
</pre>

The key part is the (sec)urity setting. Then test if this has worked by running commands like <code>df -h</code> or <code>mount</code>. If it has been successful, then you need to make it persistant by adding the following entry to the <code>/etc/fstab</code> file:

<pre>
nfs-storage.cb.net:/nfs/export_rw   /mnt/backups    nfs    soft,timeo=100,_netdev,rw,<strong>sec=krb5</strong>   0   0
</pre>


[post-content post_name=rhsca-quiz] 

The following questions relates to making changes to the <strong>nfs server</strong>:
[accordion]
[toggle title="What additional tasks needs to be done on the NFS server?"]
- add nfs principal for the host
- update /etc/exports file with kerberos option
- restart nfs daemon
[/toggle]
[toggle title="What is the command to add the nfs principal for nfs server, nfs-storage.cb.net?"]
addprinc -randkey nfs/nfs-storage.cb.net
ktadd nfs/nfs-storage.cb.net
quit
[/toggle]
[toggle title="What changes needs to be added to /etc/exports?"]
add 'sec=krb5' as another option inside the round brackets
[/toggle]
[toggle title="what is the command to restart the nfs server daemon?"]
$ systemctl restart nfs-server
[/toggle]
[toggle title="Where can you check whether the krb feature has been enabled in nfs-server?"]
$ cat /var/lib/nfs/etab
[/toggle]
[/accordion]

The following questions relates to doing tasks on the <strong>nfs client</strong>, called nfs/nfs-client.cb.net:
[accordion]
[toggle title="What tasks needs to be performed on the nfs client?"]
- add principals for nfs
- restart+enable the nfs client target
- Do a manual mount using mount command - this is done for testing purposes
- add entry to fstab file
[/toggle]
[toggle title="What are the kadmin commands for adding nfs principle"]
addprinc -randkey nfs/nfs-client.cb.net
ktadd nfs/nfs-client.cb.net
quit
[/toggle]
[toggle title="What is the command to restart+enable the nfs client target?"]
$ systemctl restart nfs-client.target
$ systemctl enable nfs-client.target
[/toggle]
[toggle title="What is the command to check if nfs mounting has been successful?"]
$ df -h
[/toggle]
[toggle title="question?"]
answer
[/toggle]
[/accordion]