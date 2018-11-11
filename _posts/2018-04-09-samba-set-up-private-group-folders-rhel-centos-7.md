---
ID: 3582
post_title: >
  Samba – Set up private group folders
  RHEL/CentOS 7
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhce/samba-set-up-private-group-folders-rhel-centos-7
published: true
post_date: 2018-04-09 09:54:45
---
A common scenario is to provide a samba share to a particular group of people, where each group member has full read+write access to all the content stored in the Samba share. You can follow along this article using our <a href="https://github.com/Sher-Chowdhury/CentOS7-Samba-Private-Group-demo">Samba group collaboration vagrant project on github</a>.


There's a few ways to set up group collaboration in Samba. To start with we first need to create a standard Samba Server and Samba Client. After that on the Samba Server side we need to do the following:


<ol>
	<li>Create new OS Linux group that will have access to the samba share - (server and clients)</li>
	<li>Create Linux users and add them to this group - (server and clients) </li>
        <li>Also register the newly create Linux users with the Samba - (server only)</li>
	<li>Create the new folder to be shared, and ensure that this folder is group owned by the new group, and that group has full rwx permissons - (server only)</li> 
	<li>update the smb.conf file to give the Linux group read+write access - (server only)</li>
	<li>restart the samba service - (server only)</li>
	<li>Use the mount command to test the - (client only)</li>
        <li>Setup automounting - (client only)</li>
</ol>

We'll walk through an example of how to set all this up. In our example we want to give a group called 'editors' read+write access to a samba share. this group will have 2 members, 'tom' and 'jerry'. Also we have one Samba server (samba-storage.local) and one Samba client (samba-client.local). The folder that we want to share out for group collaboration already exists on the Samba server:

<pre>
+--------------------------+          +--------------------------------+
|                          |          |                                |
|      samba-storage       |          |           samba-client         |
|     (IP: 10.1.4.10)      |          |                                |
|                          |          |                                |
|                          |          |                                |
|                          |          |                                |
|  +------------------+    |          |  +--------------------------+  |
|  | /samba/editors   |<---------------->| /mnt/editors-team-folder |  |
|  +------------------+    |          |  +--------------------------+  |
|                          |          |                                |
|  +----------------+      |          |   +----------------+           |
|  | Group: Editors |      |          |   | Group: Editors |           |
|  |                |      |          |   |                |           |
|  | user: tom      |      |          |   | user: tom      |           |
|  | user jerry     |      |          |   | user jerry     |           |
|  +----------------+      |          |   +----------------+           |
|                          |          |                                |
+--------------------------+          +--------------------------------+

</pre>


First off let's create our 'editors' group on both our Samba Server and Samba Clients. 
 
<pre>
$ groupadd --gid 2000 editors
</pre>

Here we chose the gid of 2000. But you can choose any value. The important thing here is that this GID value is the same across all boxes. I chose 2000 after first checking that 2000 isn't referenced in the <code>/etc/group</code> file of our Samba server or any Samba clients. 

Next we create the group members on both the Samba server and Samaba clients  

<pre>
$ useradd -u 3334 tom 
$ useradd -u 3335 jerry

</pre>

Once again we specify the UID just so that we ensure the users have the same respective UIDs across all boxes. You can set passwords for these Linux user accounts using the <code>passwd {username}</code> command. Next we add the users to the the group, so on all boxes we need to do:


<pre>
$ usermod -aG editors tom
$ usermod -aG editors jerry
</pre>


After doing all this, all our boxes will have the user tom (with uid of 3334) and jerry (with uid of 3335) and they both belong to a group called 'editors' (with gid of 2000). 


Next we need to perfom a few task on the Samba server. To start with Our share needs to be owned by our new group and the group needs to be given full permissions:

<pre>
[root@samba-storage ~]# mkdir -p /samba/editors
[root@samba-storage ~]# chgrp editors /samba/editors
[root@samba-storage ~]# chmod g+rwx /samba/editors
</pre>




Next we need to configure the smb.conf file on the Samba Server so to give read+write permissions to members of the editors group.  under you share's stanza, you need to update the <code>valid users</code> and <code>write list</code> settings to refer to the sambagroup group using the '@' notation:


<pre>
  valid users = @editors
  write list = @editors
</pre>

We also need to append the following setting too:

<pre>
  force group = editors
  create mask = 0770
</pre>

Here, <strong>force group</strong> means we are forcing any files+folders created under /samba/editors folder to be group-owned by the editors group. An alternative (or in addition) to using <strong>force group</strong> setting is to just apply the sgid setting on the share, i.e. <code>chmod g+s /samba/share</code>. So overall, our smb.conf stanza looks something like this:

<pre>
[editors_collab_folder]
  comment = editors team folder  
  path = /samba/editors
  available = yes
  writable = yes
<strong>  valid users = @editors
  write list = @editors</strong>
  read only = no
  browseable = yes
  public = yes
<strong>  force group = editors
  create mask = 0770</strong>
</pre>


Next on the Samba server, we need to register each group member with Samba (this is so to do a workaround for file-ownership issue which is covered further down):

 
<pre>
[root@samba-storage ~]# smbpasswd -a tom
[root@samba-storage ~]# smbpasswd -a jerry
</pre>


Next we need to load in these changes by restarting the daemon:


<pre>
$ systemctl restart smb.service
</pre>




On the client side we now need to see if we can access the new share. For now we'll use tom's samba credentials. 


<pre>
[root@samba-client ~]# smbclient -L //samba-storage.local -U tom
Enter SAMBA\tom's password:
Domain=[SAMBA-STORAGE] OS=[Windows 6.1] Server=[Samba 4.6.2]

	Sharename       Type      Comment
	---------       ----      -------
	print$          Disk      Printer Drivers
	editors_collab_folder Disk      editors team folder
	IPC$            IPC       IPC Service (Samba server samba-storage)
	tom             Disk      Home Directories
Domain=[SAMBA-STORAGE] OS=[Windows 6.1] Server=[Samba 4.6.2]

	Server               Comment
	---------            -------

	Workgroup            Master
	---------            -------
</pre>


We can now try to mount this manually:

<pre>
$ mount -t cifs -o user=tom,password=tompassword,rw,soft,_netdev //samba-storage.local/export_rw /mnt/editors-team-folder
</pre>

Then try automounting it by adding the following line in the fstab:


<pre>
$ cat /etc/fstab

#
# /etc/fstab
# Created by anaconda on Sat Mar 24 21:49:59 2018
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/centos-root /                       xfs     defaults        0 0
UUID=6bd03c61-c02b-4d0f-9951-1c0c84c784d8 /boot                   xfs     defaults        0 0
/dev/mapper/centos-home /home                   xfs     defaults        0 0
/dev/mapper/centos-swap swap                    swap    defaults        0 0
<strong>//samba-storage.local/editors_collab_folder /mnt/editors-team-folder  cifs  credentials=/etc/toms-samba-creds,soft,rw,_netdev  0 0</strong>
</pre>


and then create the following credentials file:


<pre>
[root@samba-client ~]# ll /etc/toms-samba-creds
-rw-------. 1 root root 34 Apr 13 09:52 /etc/toms-samba-creds
[root@samba-client ~]# cat /etc/toms-samba-creds
username=tom
password=tompassword 
</pre>


Now we can test this out by switching to user tom and try creating a file. This files becomes 

After that you can test this setup by creating a file as user tom:

<pre>
[tom@samba-client ~]$ touch /mnt/editors-team-folder/file-created-by-tom
[tom@samba-client ~]$ ll /mnt/editors-team-folder/file-created-by-tom
-rw-rw----. 1 tom editors 0 Apr 13 10:32 /mnt/editors-team-folder/file-created-by-tom
</pre>

As you can see, the new file is owned by tom, and group owned by the editors group, which also has full read+write privileges to this file. Which means another group member e.g. jerry, can change this file. However, oddly, when jerry creates a new file, you'll discover that the new files ends up getting owned by tom!:

<pre>
[jerry@samba-client ~]$ touch /mnt/editors-team-folder/file-created-by-jerry
[jerry@samba-client ~]$ ll /mnt/editors-team-folder/file-created-by-jerry
-rw-rw----. 1 <strong>tom</strong> editors 0 Apr 13 10:38 /mnt/editors-team-folder/file-created-by-jerry
</pre>

That's because the samba share was mounted using tom's samba credentials. This can be a bit confusing, although ultimately this Samba share is still functional since it doesn't really matter who owns the file, as long as access is restricted to only the group members and only the group members have full read+write permissions. Having said that, there are a couple of ways to workaround this problem:

<ul>
	<li>add multiple entries in fstab, one for each group member - to mount the same samba share. E.g. for jerry, we create a mount point, /home/jerry/editors-team-folder. This is why it's important to that we registered each group member to samba using the smpasswd command. Another option could be to do the automouning via each user's .profile</li>
	<li>Create a generic service account, e.g. samba_share and mount folders using the service account. Then inform the group members that all file ownership will default to this user</li>
</ul>



   

<h2>Problems with this setup</h2>
The approach we took in this article to setup Group Collaboration is actually quite crude. For example:

- Making things like gids and uids consistent across all boxes is a pain, especially if you have lots of users and Samba clients. 
- file/ownerships are not consistent - so have to use workarounds as described earlier. 

The solution to these problems is to use a centralised system for storing user account details and authentication I.e the solution is to use ldap, openldap or freeipa. Using ldap will mean all username uids and group gids are all stored in a single central place. In addition Samba supports ldapsam (ldap security account manager) as a passdb backend. So you can use that instead of the more crude smbpasswd approach.   


[post-content post_name=rhce-quiz] 

[accordion]
[toggle title="What tasks do you need to do on top of the standard samba server-client setup?"]
<ol>
	<li>Create new OS Linux group that will have access to the samba share - (server and clients)</li>
	<li>Create Linux users and add them to this group - (server and clients) </li>
        <li>Also register the newly create Linux users with the Samba - (server only)</li>
	<li>Create the new folder to be shared, and ensure that this folder is group owned by the new group, and that group has full rwx permissons - (server only)</li> 
	<li>update the smb.conf file to give the Linux group read+write access - (server only)</li>
	<li>restart the samba service - (server only)</li>
	<li>Use the mount command to test the - (client only)</li>
        <li>Setup automounting - (client only)</li>
</ol>
[/toggle]
[toggle title="What is the important thing to ensure when creating the Linux group and group members across all boxes?"]
- the group needs to have the same gid across all boxes - hence need to explicitly specify gid value when using the groupadd command
- the user account needs to have the same uid values across all boxes - hence need to explicitly specify the uid value when using the useradd command. 
[/toggle]
[toggle title="What do you need to ensure about the folder to be shared?"]
- needs to be owned by the newly created group
[/toggle]
[toggle title="What 4 stanza settings do you need to ensure is present in the smb.conf file?"]
- valid users = @{group-name}
- write list = @{group-name}
- force group = {group-name}
- create mask = 0770
[/toggle]
[toggle title="question?"]
answer
[/toggle]
[toggle title="question?"]
answer
[/toggle]
[toggle title="question?"]
answer
[/toggle]
[/accordion]