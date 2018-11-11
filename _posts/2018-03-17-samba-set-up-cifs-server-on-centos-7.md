---
ID: 455
post_title: >
  Samba – Set up CIFS Server on
  RHEL/CentOS 7
author: sher
post_excerpt: >
  If you have directories on your machine
  that you want to share out to other
  machines, including Windows based
  machine then you can do this by setting
  up your machine as a Samba/CIFS server.
layout: post
permalink: >
  https://codingbee.net/tutorials/rhce/samba-set-up-cifs-server-on-centos-7
published: true
post_date: 2018-03-17 00:00:00
---
If you have directories on your machine that you want to share out to other machines then you can do this by <a href="http://codingbee.net/tutorials/rhce/nfs-set-up-an-nfs-server-on-centos-rhel-7">setting up your machine as an NFS server</a>. However with NFS you can only share out folders to machine that are also in the same private network. If you want share folders to other machines over the public internet, then that's where you need to use the Samba/CIFS protocol.  You can follow along this article using this <a href="https://github.com/Sher-Chowdhury/CentOS7-Samba-demo">Samba vagrant project on Github</a>. 

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


This article only covers setting up the samba server (which in this example is called samba-storage). We created a separate article on <a href="https://codingbee.net/tutorials/rhce/samba-how-to-set-up-a-samba-client-on-centos-rhel-7-2">how to set up a samba client</a>. 

First you need to install: 

<pre>
$ yum install samba samba-client
</pre>


For SELinux, we need to enable a few SEBoolean settings:

<pre>
$ getsebool -a | grep samba | grep export
samba_export_all_ro --> off
samba_export_all_rw --> off
$ getsebool -a | grep samba | grep share | grep nfs
samba_share_nfs --> off
</pre>

To (p)ersistantly enable these settings we do:

<pre>
$ setsebool -P samba_export_all_ro on
$ setsebool -P samba_export_all_rw on
$ setsebool -P samba_share_nfs on
</pre>



Next we create a folder that will get shared out (aka exported):

<pre>
$ mkdir -p /samba/export_rw
$ ll -dZ /samba/export_rw
drwx<strong>r-x</strong>r-x. <strong>root root</strong> unconfined_u:object_r:<strong>default_t</strong>:s0 /samba/export_rw
</pre>

There's a few things we need to fix for this folder:


<ol>
	<li>need to change the owner and group owner</li>
	<li>need to give full permissions to the group</li>
	<li>SELinux type attribute needs to be set to samba_share_t</li>
</ol>


Let's create a new user which will access this samba share: 


<pre>
$ useradd samba_user1
</pre>

Next we give this owner of this folder:

<pre>
chown samba_user1:samba_user1 /samba/export_rw
</pre>

Next the SELinux type attribute needs to be set to <strong>samba_share_t</strong>, so in our case we do:

<pre>
$ semanage fcontext -at samba_share_t "/samba/export_rw(/.*)?"
</pre>

Then apply this new rule to our export:

<pre>
$ restorecon -R /samba/export_rw
$ ll -dZ /samba/export_rw
drwxrwxr-x. samba_user1 samba_user1 unconfined_u:object_r:samba_share_t:s0 /samba/export_rw
</pre>



Next we need allow samba traffic through firewalld:


<pre>
$ firewall-cmd --permanent --add-service=samba
$ systemctl restart firewalld
</pre>

Next we need to configure the main samba config file, <code>/etc/samba/smb.conf</code>. To help with understanding  how to configure this file, you should take a look at:


<pre>
$ cat /etc/samba/smb.conf.example   
# also see:
$ man smb.conf    # this is a really long man page!
</pre>

Before we start editing this file, let's first create a backup:


<pre>
$ cp /etc/samba/smb.conf /etc/samba/smb.conf.orig
</pre>



There's two things we need to do in this file, first we edit the global section so it contains the following entries:

<pre>
[global]
        workgroup = SAMBA
        <strong>security = user
        server string = Samba server %h
        hosts allow = 127. 10.0.4.
        interfaces = lo enp0s3 enp0s8
        passdb backend = smbpasswd:/etc/samba/smbpasswd.txt</strong>
        printing = cups
        printcap name = cups
        load printers = yes
        cups options = raw
</pre>


At the very end of your <code>/etc/samba/smb.conf</code>, we need to add a section for our exported folder:


<pre>
[bckp_storage]
  comment = Folder for storing backups
  read only = no
  available = yes
  path = /samba/export_rw
  public = yes
  valid users = samba_user1
  write list = samba_user1
  writable = yes
  browseable = yes
</pre>


Descriptions for these settings are given in the man page for smb.conf. These are pretty much self explanatory, but you can find more info in the smb.conf man page. Note, in the man page all the parameters are labelled as either (G)lobal or (S)hares depending on which section they are allowed to be used in.

Next you can run the following to do a quick configtest:

<pre>
$ testparm
Load smb config files from /etc/samba/smb.conf
rlimit_max: increasing rlimit_max (1024) to minimum Windows limit (16384)
Processing section "[homes]"
Processing section "[printers]"
Processing section "[print$]"
Processing section "[bckp_storage]"
Loaded services file OK.
Server role: ROLE_STANDALONE
</pre>
 



Next we need to (a)dd our user to the samba user database and give it a samba specific password. We do this by running:

<pre>
$ smbpasswd -a samba_user1
New SMB password:
Retype new SMB password:
startsmbfilepwent_internal: file /etc/samba/smbpasswd.txt did not exist. File successfully created.
Added user samba_user1.
</pre>

Note, smbpasswd command only works for existing machine level user accounts. If the OS level user doesn't exist then you get a 'Failed to add entry for user xxxxxx' error message. Also this machine level user only needs to exist on the Samba server itself, and not the Samba client.  

To confirm this user now exists in the samba database, you can run the following command to (v)erbosely (L)ist all users:

<pre>
$ pdbedit -Lv
---------------
Unix username:        samba_user1
NT username:
Account Flags:        [U          ]
User SID:             S-1-5-21-1438882573-2886693097-939080548-3004
Primary Group SID:    S-1-5-21-1438882573-2886693097-939080548-513
Full Name:
Home Directory:       \\samba-storage\samba_user1
HomeDir Drive:
Logon Script:
Profile Path:         \\samba-storage\samba_user1\profile
Domain:               SAMBA-STORAGE
Account desc:
Workstations:
Munged dial:
Logon time:           0
Logoff time:          never
Kickoff time:         never
Password last set:    Sat, 17 Mar 2018 12:20:58 UTC
Password can change:  Sat, 17 Mar 2018 12:20:58 UTC
Password must change: never
Last bad password   : 0
Bad password count  : 0
Logon hours         : FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF

</pre>


Now we can go ahead and start the samba daemon:


<pre>
$ systemctl enable smb
ln -s '/usr/lib/systemd/system/smb.service' '/etc/systemd/system/multi-user.target.wants/smb.service'
$ systemctl start smb      
</pre>

Now you can locally test whether your new export is now available:


<pre>
[root@samba-storage samba]# smbclient -L //localhost -U samba_user1
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


This shows that the Samba share 'bckp_storage' is now ready to be shared out. The next thing to do is to <a href="http://codingbee.net/tutorials/rhcsa/samba-how-to-set-up-a-samba-client-on-centos-rhel-7">set up a Samba Client to access this share</a>. 




<h2>Summary</h2>

There's a lot of steps involved in setting up a samba server. So let's summarise all these steps:

01. install rpms
02. enable 3 sebool settings. 
03. create the Linux user that will access the samba share. 
04. create folder to share
05. change folders ownership to samba user 
06. change folder chmod perms
07. update selinux file context for the folder
08. update the main samba config file - global settings a
09. update the main samba config file - by adding new stanza
10. do config test of updated samba config file. 
11. create samba password for the samba user. 
12. check samba password database to confirm the samba user now exists. 
13. update firewall
14. start and enable the samba daemon. 
15. Check if samba share is now available.  

[post-content post_name=rhsca-quiz] 

In this quiz, we'll assume the folder we want to share is /samba/export_rw. 
[accordion]
[toggle title="What are the steps to setting up the samba server?"]
01. install rpms
02. enable 3 sebool settings. 
03. create the Linux user that will access the samba share. 
04. create folder to share
05. change folders ownership to samba user 
06. change folder chmod perms
07. update selinux file context for the folder
08. update the main samba config file - global settings a
09. update the main samba config file - by adding new stanza
10. do config test of updated samba config file. 
11. create samba password for the samba user. 
12. check samba password database to confirm the samba user now exists. 
13. update firewall
14. start and enable the samba daemon. 
15. Check if samba share is now available. 
[/toggle]
[toggle title="What rpms do you need to install?"]
$ yum install samba samba-client
[/toggle]
[toggle title="What SE booleans do you need to set?"]
$ setsebool -P samba_export_all_ro on
$ setsebool -P samba_export_all_rw on
$ setsebool -P samba_share_nfs on
[/toggle]
[toggle title="What is the command to create the samba user samba_user1?"]
$ useradd samba_user1
[/toggle]
[toggle title="What tasks needs to be done in order for /samba/export_rw folder to be exported?"]
- need to change ownership so that the samba user owns and groups owns this folder. 
- need to change ugo permissions
- need to apply the correct SELinux settings
[/toggle]
[toggle title="What are the commands to achieve these settings?"]
$ semanage fcontext -at samba_share_t "/samba/export_rw(/.*)?"
$ restorecon -Rv /samba/export_rw
$ chown samba_user1:samba_user1 /samba/export_rw
$ chmod 775 /samba/export_rw
[/toggle]
[toggle title="What settings needs to exist under the global section of /etc/samba/smb.conf?"]
- security = user
- server string = samba server %h
- hosts allow = 127. 192.10.10
- interfaces = lo enp0s8
- passdb backend = smbpasswd:/etc/samba/smbpasswd.txt
[/toggle]
[toggle title="What additional stanza needs to created for our share?"]
[meaningful_name]
- comment = this is a samba share
- read only = no
- available = yes
- path = /samba/export_rw
- public = yes
- valid users = samba_user1
- writable = yes
- write list = samba_user1
- browsable = yes
[/toggle]
[toggle title="What is the command to do a config test?"]
$ testparm
[/toggle]
[toggle title="What is the command to create samba password for the samba_user?"]
$ smbpasswd -a samba_user1
# this ends up creating the file that's defined in the smb.conf
[/toggle]
[toggle title="What is the command to check whether this user is now registered in the smbpasswd database?"]
$ pdbedit -Lv
[/toggle]





[toggle title="What firewalls needs to be opened?"]
$ firewall-cmd --permanent --add-service=samba
$ systemctl restart firewalld
[/toggle]

[toggle title="Now what is the command to start and enable the samba service?"]
$ systemctl status smb
$ systemctl start smb
[/toggle]
[toggle title="What is the command to do a quick sanity check to see if our Samba share is now available?"]
$ smbclient -L //localhost -U samba_user1
# you will get prompted to enter the smbpasswd. 
[/toggle]
[/accordion]