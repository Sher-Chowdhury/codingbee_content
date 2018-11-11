---
ID: 471
post_title: >
  Automounting remotely shared folders
  (NFS and CIFS)
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-automounting-remotely-shared-folders-nfs-and-cifs
published: true
post_date: 2015-10-24 00:00:00
---
<h2>Overview of automounting NFS and CIFS shares</h2>

Mounting CIFS and NFS shares using the mount command, won't survive a reboot. Luckily there are three different ways to auto-mount CIFS and NFS shares when the machine boots up:

<ul>
	<li>Adding entries to /etc/fstab</li>
	<li>via autofs</li>
	<li>via systemd</li>
</ul>

By the end of this article you should be able to answer the following questions:
 
[accordion]
[toggle title="01. How many different ways can you automount, and what are they?"]
There are 3 ways, the are:
- Adding entries to /etc/fstab
- via autofs
- via systemd
[/toggle]
[toggle title="02. What is the fstab entry you need to add to mount the The-Server's, NFS shared folder, '/nfs/Shared-Folder' onto the mountpoint '/nfs/The-Server/Shared-Folder'?"]
<span style='font-size:12px'>The-Server:/nfs/Shared-Folder   /nfs/The-Server/Shared-Folder   nfs   defaults   0   0</span>[/toggle]
[toggle title="03. What is the fstab entry  you need to add to mount the The-Server's, CIFS shared folder, 'Shared-Folder' onto the mountpoint '/cifs/The-Server/Shared-Folder', where the user credentials for accessing the cifs is username=vagrant and password=admin?"]
<span style='font-size:12px'><mark>//</mark>The-Server/Shared-Folder   /cifs/The-Server/Shared-Folder   cifs   username=vagrant,password=admin  0  0</span>
# remember to use the "//" syntax again. 
[/toggle]
[toggle title="04. What is the command to check that your fstab entries have worked?"]
$ mount -a
[/toggle]
[toggle title="05. What are 2 commands to check that your nfs/cifs have been successfully mounted?"]
$ mount
# or 
$ df -h
[/toggle]
[toggle title="06. What is the command to install the automatic mounting software?"]
$ yum install autofs
[/toggle]
[toggle title="07. Then what do you need to do?"]
# start the service
$ systemctl start autofs
[/toggle]
[toggle title="08. Where is this package's main config file located?"]
/etc/auto.master
[/toggle]
[toggle title="09. What entry do you need to add to this to indicate that the folder /nfs/The-Server will host remote folders, and this folder's configuration will be stored in our custom config file, which will be /etc/autofs/nfs/The-Server.conf?"]
/nfs/The-Server   /etc/autofs/nfs/The-Server.conf
[/toggle]
[toggle title="10. What entry do you need to add to /etc/autofs/nfs/The-Server.conf, in order to automount the nfs share, /nfs/Shared-Folder, that is being shared by 'The-Server' onto the mountpoint /nfs/The-Server/A-Shared-Folder?"]
A-Shared-Folder  -fstype=nfs,rw  The-Server:/nfs/Shared-Folder
[/toggle]
[toggle title="11. Then what do you need to do?"]
# restart the service
$ systemctl restart autofs
[/toggle]
[toggle title="12. Then what do you need to do?"]
# cd into the mount point
$ cd /nfs/The-Server/A-Shared-Folder
[/toggle]
[toggle title="13. What is the command to check this has worked?"]
$ mount 
# or 
$ df -h
[/toggle]
[toggle title="14. What entry do you need to add to /etc/auto.master to indicate that the folder /cifs/The-Server will host remote folders, and this folder's configuration will be stored in our custom config file, which will be /etc/autofs/cifs/The-Server.conf?"]
/cifs/The-Server /etc/autofs/cifs/The-Server.conf
[/toggle]
[toggle title="15. What entry do you need to add to /etc/autofs/cifs/The-Server.conf, in order to automount the cif share, cifshare1, that is being shared by 'The-Server' onto the mountpoint /cifs/The-Server/Shared-Folder?"]<span style="font-size:12px">Shared-Folder  -fstype=cifs,rw,username=vagrant,password=admin  <mark>://</mark>The-Server/cifshare1
# note don't forget to include the "//", also notice that this time you also need to prefix the colon as well ":". Therefore you now end up with "://". It looks wrong but that's how it is.</span> 
[/toggle]
[toggle title="16. Then what do you need to do?"]
# restart the service
$ systemctl restart autofs
[/toggle]
[toggle title="17. Then what do you need to do?"]
# cd into the mount point
$ cd /cifs/The-Server/Shared-Folder
[/toggle]
[toggle title="18. What is the command to check this has worked?"]
$ mount 
# or 
$ df -h
[/toggle]
[toggle title="What is the command - need to add questions for the systemd approach?"]


[/toggle]
[toggle title="What is the command?"]


[/toggle]
[toggle title="What is the command?"]


[/toggle]
[toggle title="What is the command?"]


[/toggle]
[toggle title="What is the command?"]


[/toggle]
[/accordion]
<hr/>

<h2>Automounting using fstab</h2>

Quick recap, here's how we manually mounted the shared folders from our previous article's examples for nfs and cifs respectively:

<pre>
<span style='font-size:12px'>$ mount -t nfs The-Server:/nfs/Shared-Folder /nfs/The-Server/Shared-Folder
$ mount -t cifs -o user=vagrant,password=admin //The-Server/Shared-Folder /cifs/The-Server/Shared-Folder</span>
</pre>


<h3>Automount NFS via the fstab file</h3>

Following on from the earlier example. 

To automatically mount an export during boot time, you need to add the following entry into the <code>/etc/fstab</code> config file.

<pre>
<span style='font-size:12px'>The-Server:/nfs/Shared-Folder /nfs/The-Server/Shared-Folder nfs defaults 0 0</span>
</pre>

To check that we haven't made a mistake, we should run the following to confirm we have done everything correctly:


<pre>
$ mount -a
</pre>


<h3>Automount CIFS using fstab</h3>


Now here's the entry we need to make in the <code>/etc/fstab</code> file for this example:


<pre>
<span style='font-size:12px'>//The-Server/Shared-Folder  /cifs/The-Server/Shared-Folder  cifs  username=vagrant,password=admin  0 0</span>
</pre>


However to ensure you haven't made a mistake. Check that the CIFS share is currently mounted. And then run:

<pre>
$ mount -a
</pre>

Then check if the CIFS share is now mounted. If it has then your fstab cif entry is correct. 

Note, in this approach, we have added credentials as plain texts in the fstab file. To make it a bit more secure, you can take this approach instead:

<pre>
<span style='font-size:12px'>//The-Server/Shared-Folder  /cifs/The-Server/Shared-Folder  cifs  credentials=~/.secret.txt  0 0</span>
</pre>

Where the credentials files contains:


<pre>$ cat /root/.secret.txt
username=vagrant
password=admin
</pre>


<h2>Automounting using autofs</h2>
Autofs is a more intelligent alternative to using fstab. That's because Autofs has 2 main advantages:

<ol>
	<li>mounted remote resources are automatically unmounted after a period of inactivity.</li>
	<li>unmounted remote resources are automatically mounted again when you try to access the mount-point.</li>
	<li>You don't have to create the mount point before hand, only the mountpoint's parent directories</li>

</ol>

To be able to do automounting, you need to first install the automounting package which is called autofs:

<pre>
$ yum install autofs
</pre>


The main autofs config file is auto.master:


<pre>
$ cat /etc/auto.master
#
# Sample auto.master file
# This is a 'master' automounter map and it has the following format:
# mount-point [map-type[,format]:]map [options]
# For details of the format look at auto.master(5).
#
/misc   /etc/auto.misc
#
# NOTE: mounts done from a hosts map will be mounted with the
#       "nosuid" and "nodev" options unless the "suid" and "dev"
#       options are explicitly given.
#
/net    -hosts
#
# Include /etc/auto.master.d/*.autofs
# The included files must conform to the format of this file.
#
+dir:/etc/auto.master.d
#
# Include central master map if it can be found using
# nsswitch sources.
#
# Note that if there are entries for /net or /misc (as
# above) in the included master map any keys that are the
# same will not be seen as the first read key seen takes
# precedence.
#
+auto.master
</pre>

Now we'll take a look at how to configure autofs to automount nfs and cifs shares, starting with nfs.

<h3>Automount NFS via autofs</h3>
The auto.master file itself doesn't contain mounting related info. Instead it's a file that maps each mountpoint's parent folder along with location of a custom config file that houses actual mounting info. That's why the entries in this file is in the form of 2 column entries. Therefore for this example, we added the following line to the /etc/auto.master file:

<pre>
/nfs/The-Server   /etc/autofs/nfs/The-Server.conf
</pre>


Note: The "/nfs/The-Server" folder isn't a mount point itself, but houses mount points. This folder is therefore sometime srefered to as a "automount" folder, i.e. the mount point's parent folder.   



The second entry could be anything to your liking. However it's best practice to try to make it meaningful. Now let's create our custom file:

<pre>
$ mkdir -p /etc/autofs/nfs
$ touch /etc/autofs/nfs/The-Server.conf
</pre>

Now in this file custom file, we add the following:

<pre>
$ cat /etc/autofs/nfs/The-Server.conf
A-Shared-Folder  -fstype=nfs,rw  The-Server:/nfs/Shared-Folder
</pre>

Here we just specify the mountpoint folder, and not the full path. In this example our mountpoint's name is "A-Shared-Folder"

Now we restart autofs service:

<pre>
$ systemctl enable autofs
ln -s '/usr/lib/systemd/system/autofs.service' '/etc/systemd/system/multi-user.target.wants/autofs.service'
$ systemctl start autofs
</pre>

Now let's check our mount point. 
 
<pre>
$ pwd
/nfs/The-Server
$ ls -l
total 0
$ cd A-Shared-Folder
$ ls -l
total 0
drwxr-xr-x. 2 root root 26 Oct 24 20:14 subfolder
-rw-r--r--. 1 root root  0 Oct 24 20:14 testfile1.txt
</pre>

Notice how we can magically cd into the mountpoint, even though it didn't originally exist. 


At first it seems a bit unusual, the structure of the auto.master file being a mapping to a mountpoint's parent and it's corresponding config file. This is designed for easier scalability. For example, let's say we have 2 nfs shares available on The-Server, instead of one:


<pre>
$ showmount -e The-Server
Export list for The-Server:
/nfs/Shared-Folder2 *             # this is a new nfs share.  
/nfs/Shared-Folder  *
</pre>


Then automount this, we don't need to edit auto.master. Instead we add another entry to our custom file:

<pre>
$ cat /etc/autofs/nfs/The-Server.conf
A-Shared-Folder  -fstype=nfs,rw  The-Server:/nfs/Shared-Folder
Another-Shared-Folder -fstype=nfs,rw  The-Server:/nfs/Shared-Folder2
</pre>

As you can see, the mountpoint's parent folder defined in /etc/auto.master actually becomes the home of multiple possible NFS mountpoints. In our case both NFS shares originated from the same server, we could have had different nfs servers, in which case it would instead be better for a different parent mount folder name, e.g. just "/nfs".  


Now let's test this out:


<pre>
$ pwd
/nfs/The-Server
$ ls -l
total 0
$ cd A-Shared-Folder
$ ls
subfolder  testfile1.txt
$ pwd
/nfs/The-Server/A-Shared-Folder
$ cd ..
$ ls
A-Shared-Folder
$ cd Another-Shared-Folder
$ pwd
/nfs/The-Server/Another-Shared-Folder
$ ls -l
total 4
-rw-r--r--. 1 root root 6 Oct 28 21:05 yet-more-stuff.txt
$ cd ..
$ pwd
/nfs/The-Server
$ ls -l
total 0
drwxr-xr-x. 2 root root 31 Oct 28 21:05 Another-Shared-Folder
drwxr-xr-x. 3 root root 42 Oct 24 20:14 A-Shared-Folder

</pre>


<h3>Automount CIFS via autofs</h3>

Quick recap:

<pre>
$ smbclient -L The-Server --user vagrant
Enter vagrant's password:
Domain=[MYGROUP] OS=[Unix] Server=[Samba 4.1.12]

        Sharename       Type      Comment
        ---------       ----      -------
        Shared-Folder   Disk
        IPC$            IPC       IPC Service (Samba Server Version 4.1.12)
        vagrant         Disk      Home Directories
Domain=[MYGROUP] OS=[Unix] Server=[Samba 4.1.12]

        Server               Comment
        ---------            -------

        Workgroup            Master
        ---------            -------
</pre>

In a similar fashion to nfs, we first add the following line to /etc/auto.master:

<pre>
/cifs/The-Server /etc/autofs/cifs/The-Server.conf
</pre> 

And in the custom config file we add:

<pre>
Shared-Folder  -fstype=cifs,rw,username=vagrant,password=admin  ://The-Server/Shared-Folder
</pre>


Now, let's test if it this worked:



<pre>
$ pwd
/cifs/The-Server
$ ls -l
total 0
$ cd Shared-Folder
$ pwd
/cifs/The-Server/Shared-Folder
$ ls -l
total 0
-rwxrwxrwx. 1 root    root    0 Oct 24 22:31 hello-world-cifs.txt
drwxrwxrwx. 2 root    root    0 Oct 24 22:31 subfolder
-rw-r--r--. 1 vagrant vagrant 0 Oct 28 21:51 testfile.txt

</pre>

Success!

<h2>Automounting using systemd</h2>

This is relatively new approach. With this approach you can't have any "-" characters in the full path of your mount point. Then it's just a case of creating the .mount file, and using systemctl to start+enable your new .mount unit. 


<h3>Automounting NFS using systemd</h3>


First we create the mountpoint:

<pre>
$ mkdir -p /nfs/TheServer/SharedFolder
</pre>


Now we create the following unit file:



<pre>
$ cat /etc/systemd/system/nfs-TheServer-SharedFolder.mount
[Unit]
Description=NfS share from The-Server

[Mount]
What=The-Server:/nfs/Shared-Folder
Where=/nfs/TheServer/SharedFolder
Type=nfs
Options=rw

[Install]
WantedBy=multi-user.target
</pre>


Finally we start+enable this mount unit:

<pre>
$ systemctl start nfs-TheServer-SharedFolder.mount
$ systemctl enable nfs-TheServer-SharedFolder.mount
$ systemctl status nfs-TheServer-SharedFolder.mount
nfs-TheServer-SharedFolder.mount - Nfs share form The-Server
   Loaded: loaded (/etc/systemd/system/nfs-TheServer-SharedFolder.mount; enabled)
   Active: active (mounted) since Wed 2015-10-28 23:14:23 GMT; 2min 13s ago
    Where: /nfs/TheServer/SharedFolder
     What: The-Server:/nfs/Shared-Folder

Oct 28 23:14:23 puppetmaster.local systemd[1]: Mounting Nfs share form The-Server...
Oct 28 23:14:23 puppetmaster.local systemd[1]: Mounted Nfs share form The-Server.
Oct 28 23:16:29 puppetmaster.local systemd[1]: Mounted Nfs share form The-Server.
</pre>


Now, we can go in to check it has worked:

<pre>
$ cd /nfs/TheServer/SharedFolder
$ ls -l
total 0
drwxrwxrwx. 2 root      root      26 Oct 24 20:14 subfolder
-rw-r--r--. 1 nfsnobody nfsnobody  0 Oct 28 23:02 testfile
-rwxrwxrwx. 1 root      root       0 Oct 24 20:14 testfile1.txt
</pre>
 
Also to confirm it has worked, we can do:

<pre>
$ df -h
Filesystem                            Size  Used Avail Use% Mounted on
/dev/mapper/centos_puppetmaster-root   38G  4.7G   33G  13% /
devtmpfs                              487M     0  487M   0% /dev
tmpfs                                 497M   80K  497M   1% /dev/shm
tmpfs                                 497M  6.9M  490M   2% /run
tmpfs                                 497M   16K  497M   1% /sys/fs/cgroup
The-Server:/nfs/Shared-Folder          38G  3.9G   34G  11% /nfs/TheServer/SharedFolder
$ mount | grep The-Server
/etc/autofs/nfs/The-Server.conf on /nfs/The-Server type autofs (rw,relatime,fd=-1,pgrp=15130,timeout=300,minproto=5,maxproto=5,indirect)
The-Server:/nfs/Shared-Folder on /nfs/TheServer/SharedFolder type nfs4 (rw,relatime,vers=4.0,rsize=131072,wsize=131072,namlen=255,hard,proto=tcp,port=0,timeo=600,retrans=2,sec=sys,clientaddr=192.168.50.10,local_lock=none,addr=192.168.50.11)

</pre>


<h3>Automounting CIFS using systemd</h3>

First we create the mount point: 

<pre>
$ mkdir -p /cifs/TheServer/SharedFolder
</pre>


Now we create the mount.unit:



<pre>
$ cat /etc/systemd/system/cifs-TheServer-SharedFolder.mount
[Unit]
Description=CIFS share from The-Server

[Mount]
What=//The-Server/Shared-Folder
Where=/cifs/TheServer/SharedFolder
Type=cifs
Options=rw,username=vagrant,password=admin

[Install]
WantedBy=multi-user.target

</pre>


Once again you name this unit after the full mountpath with the "/" replaced by "-". 


Next we start+enable it:


<pre>
$ systemctl start cifs-TheServer-SharedFolder.mount
$ systemctl enable cifs-TheServer-SharedFolder.mount
ln -s '/etc/systemd/system/cifs-TheServer-SharedFolder.mount' '/etc/systemd/system/multi-user.target.wants/cifs-TheServer-SharedFolder.mount'
$ systemctl status cifs-TheServer-SharedFolder.mount
cifs-TheServer-SharedFolder.mount - CIFS share from The-Server
   Loaded: loaded (/etc/systemd/system/cifs-TheServer-SharedFolder.mount; enabled)
   Active: active (mounted) since Wed 2015-10-28 23:31:07 GMT; 5min ago
    Where: /cifs/TheServer/SharedFolder
     What: //The-Server/Shared-Folder

Oct 28 23:31:07 puppetmaster.local systemd[1]: Mounting CIFS share from The-Server...
Oct 28 23:31:07 puppetmaster.local systemd[1]: Mounted CIFS share from The-Server.
Oct 28 23:36:36 puppetmaster.local systemd[1]: Mounted CIFS share from The-Server.
</pre>

Now we confirm that it is working:

<pre>
$ cd /cifs/TheServer/SharedFolder
$ ls -l
total 0
-rwxrwxrwx. 1 root    root    0 Oct 24 22:31 hello-world-cifs.txt
drwxrwxrwx. 2 root    root    0 Oct 24 22:31 subfolder
-rw-r--r--. 1 vagrant vagrant 0 Oct 28 21:51 testfile.txt
$ df -h
Filesystem                            Size  Used Avail Use% Mounted on
/dev/mapper/centos_puppetmaster-root   38G  4.7G   33G  13% /
devtmpfs                              487M     0  487M   0% /dev
tmpfs                                 497M   80K  497M   1% /dev/shm
tmpfs                                 497M  6.9M  490M   2% /run
tmpfs                                 497M   16K  497M   1% /sys/fs/cgroup
The-Server:/nfs/Shared-Folder          38G  3.9G   34G  11% /nfs/TheServer/SharedFolder
//The-Server/Shared-Folder             38G  3.9G   34G  11% /cifs/TheServer/SharedFolder
$ mount | grep cifs
//The-Server/Shared-Folder on /cifs/TheServer/SharedFolder type cifs (rw,relatime,vers=1.0,cache=strict,username=vagrant,domain=PUPPETAGENT01,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.50.11,unix,posixpaths,serverino,acl,rsize=1048576,wsize=65536,actimeo=1)
</pre>