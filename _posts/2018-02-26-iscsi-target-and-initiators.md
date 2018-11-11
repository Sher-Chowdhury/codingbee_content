---
ID: 2592
post_title: 'iSCSI &#8211; target and initiators'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhce/iscsi-target-and-initiators
published: true
post_date: 2018-02-26 13:00:25
---
iSCSI (internet Small Computer System Interface) is an IP based storage networking protocol that's designed for sharing block storage over the internet. iSCSI follows the Server-Client model. The Server (aka Target) makes storage available for Clients (aka Initiators) to use. 

The Target makes the storage available in the form of a block device (e.g. /dev/sdb). As a result the Initiators views the remote storage as a locally attached block device, and therefore treats the remote block device like an ordinary block device, e.g. you can run command like fdisk, pvcreate, mkfs.ext4,...etc against it. This means that when Initiator successfully establish a session with a Target, one or more block devices in the /dev folder (e.g. /dev/sdc) will suddenly appear and be ready for use.  

However, these remote block devices might not perform as well as the more traditional (locally attached) block devices. That's because remote block devices operates over a network so can suffer from data transfer lags.  


We will walk through a hello-world example of setting up a target and initiator. The end result of this example will be that a new block device, /dev/sdb, will appear on the Initiator which we will then install a filesystem on and ensure that it persistantly mounts.  We've created the a <a href="https://github.com/Sher-Chowdhury/CentOS7-iSCSI-demo" rel="nofollow">iSCSI demo vagrant project</a> so that you can follow along with this example:


<h2>Setting up a Target</h2>
First we'll setup a target. To start with we install the target iSCSI software:

<pre>
$ yum install -y targetcli
</pre>


Then we need to find a block device to share. The block device can be in any of the following forms:

<ol>
	<li>A whole physical block device, e.g. sda, sdb, xvdf...etc</li>
	<li>A partition, e.g. sda3, sdb2, xvdf2...etc </li>
	<li>A logical volume</li>
	<li>Create a special iSCSI image file of the desired size, which is known as a <a href="https://www.google.co.uk/search?safe=off&ei=ujGUWvvcEejZgAbYqppo&q=fileIO+backstore&oq=fileIO+backstore&gs_l=psy-ab.3..0i5i10i30k1j0i8i10i30k1.7565.9666.0.11455.5.4.1.0.0.0.73.267.4.4.0....0...1c.1.64.psy-ab..0.5.269...0i7i5i10i30k1.0.aPTPUabr9Yg" rel="nofollow">fileio backstore</a> - not recommended because it's performance is not that great. </li>
</ol>

In our example, we'll use the following block device:

<pre>
[root@iscsi-target ~]# file /dev/sdb
/dev/sdb: block special
</pre>

We start creating a target, by using the targetcli command, which starts it's own interactive session:


<pre style="line-height:100%;">
[root@iscsi-target ~]# targetcli
targetcli shell version 2.1.fb46
Copyright 2011-2013 by Datera, Inc and others.
For help on commands, type 'help'.

/>

</pre>

First off you can use the ls command to get an overall view:

<pre>
/> ls
o- / ..................................................................... [...]
  o- backstores .......................................................... [...]
  | o- block .............................................. [Storage Objects: 0]
  | o- fileio ............................................. [Storage Objects: 0]
  | o- pscsi .............................................. [Storage Objects: 0]
  | o- ramdisk ............................................ [Storage Objects: 0]
  o- iscsi ........................................................ [Targets: 0]
  o- loopback ..................................................... [Targets: 0]
/>
</pre>


Later on you'll find that there's a json file whose structure mirrors the above tree. This json file get's updated in the background with changes you make in this interactive session. You can traverse this tree using the cd command, and within each section you can run the 'help' command to get more contextual help.


Tip: use the tab+tab autocomplete technique extensively here to explore what commands are available in each section and more quickly construct your commands.  

Here are the tasks that needs to be performed in the targetcli:
<ol>
	<li>Register the block device</li>
	<li>Create the iqn - this is something that gets published to initiators when queried</li>
	<li>Create a LUN (Logical Unit Number) - this associates our block device with the iqn</li>
	<li>Create name for initiators to use - this creates a private name that initiators can connect to</li>
	<li>Set username and password (optional)</li>
</ol>




<strong>Step 1 - register the block device</strong>: register the /dev/sdb and tag it with a name that will be used to refer to it in  the targetcli:

<pre>
/> backstores/block create BD1 /dev/sdb
Created block storage object BD1 using /dev/sdb.



/> ls
o- / ......................................................................................... [...]
  o- backstores .............................................................................. [...]
  | o- block .................................................................. [Storage Objects: 1]
  | | o- BD1 ............................................ [/dev/sdb (2.0GiB) write-thru deactivated]
  | |   o- alua ................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ....................................... [ALUA state: Active/optimized]
  | o- fileio ................................................................. [Storage Objects: 0]
  | o- pscsi .................................................................. [Storage Objects: 0]
  | o- ramdisk ................................................................ [Storage Objects: 0]
  o- iscsi ............................................................................ [Targets: 0]
  o- loopback ......................................................................... [Targets: 0]
/>
</pre>

You can name it anything you want, in my case I labeled it BD1, short for Block-Device-1


Heres another way to do the same thing:

<pre>
/backstores> cd block
/backstores/block> create BD1 /dev/sdb
Created block storage object BD1 using /dev/sdb.
/backstores/block>
</pre>

<strong>Step 2 - create the IQN</strong>: Next we need to create a iSCS Qualified Name (IQN), which is a fully qualified name that will be used to refer to our new backstore block. This IQN is provided to the client when the client scans the server (in discovery mode) for available devices.  


So we create a new IQN with the name 'iqn.2018-02.net.cb.target:fqdn'. This is done in the iSCSI folder:

<pre>
/backstores/block> <strong>cd /iscsi</strong>
/iscsi> <strong>create iqn.2018-04.net.cb.target:fqdn</strong>


Created target iqn.2018-04.net.cb.target:fqdn.
Created TPG 1.
Global pref auto_add_default_portal=true
Created default portal listening on all IPs (0.0.0.0), port 3260.


/iscsi> ls /
o- / ......................................................................................... [...]
  o- backstores .............................................................................. [...]
  | o- block .................................................................. [Storage Objects: 1]
  | | o- BD1 ............................................ [/dev/sdb (2.0GiB) write-thru deactivated]
  | |   o- alua ................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ....................................... [ALUA state: Active/optimized]
  | o- fileio ................................................................. [Storage Objects: 0]
  | o- pscsi .................................................................. [Storage Objects: 0]
  | o- ramdisk ................................................................ [Storage Objects: 0]
  o- iscsi ............................................................................ [Targets: 1]
  | o- iqn.2018-04.net.cb.target:fqdn .................................................... [TPGs: 1]
  |   o- tpg1 ............................................................... [no-gen-acls, no-auth]
  |     o- acls .......................................................................... [ACLs: 0]
  |     o- luns .......................................................................... [LUNs: 0]
  |     o- portals .................................................................... [Portals: 1]
  |       o- 0.0.0.0:3260 ..................................................................... [OK]
  o- loopback ......................................................................... [Targets: 0]
/iscsi>
</pre>

Note: the iqn must be unique and follow a specific format which is:

<ul>
	<li>The 'iqn.' string followed by</li>
	<li>YYYY-MM. - this is usually the year the box's domain is registered, but can be any month+year, </li>
	<li>machine's hostname written in reverse order (Note: our box's hostname is 'target.cb.net'. )</li>
	<li>optional string - this is to help with making the IQN unique. In our example we gave it the string 'fqdn'</li>
</ul>

Tip: you don't need to memorise this format. You can see an example of this format, when configuring the iscsi client, where an example is given in /etc/iscsi/initiatorname.iscsi, we'll cover this further down. 

Notice something called a 'TPG1' has been created. This is short for 'Target Portal Group'. The TPG is something that groups various configurations together. We can take a deeper look like this:

<strong>Step 3 - Create a LUN</strong>: Next we need to create a LUN (Logical Unit Number). This is done inside the luns folder. 


<pre>
/iscsi> cd /iscsi/iqn.2018-02.net.cb.target:fqdn/tpg1/luns
....luns> create /backstores/block/BD1
Created LUN 0.



/iscsi/iqn.20...qdn/tpg1/luns> ls /
o- / ......................................................................................... [...]
  o- backstores .............................................................................. [...]
  | o- block .................................................................. [Storage Objects: 1]
  | | o- BD1 .............................................. [/dev/sdb (2.0GiB) write-thru activated]
  | |   o- alua ................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ....................................... [ALUA state: Active/optimized]
  | o- fileio ................................................................. [Storage Objects: 0]
  | o- pscsi .................................................................. [Storage Objects: 0]
  | o- ramdisk ................................................................ [Storage Objects: 0]
  o- iscsi ............................................................................ [Targets: 1]
  | o- iqn.2018-02.net.cb.target:fqdn .................................................... [TPGs: 1]
  |   o- tpg1 ............................................................... [no-gen-acls, no-auth]
  |     o- acls .......................................................................... [ACLs: 0]
  |     o- luns .......................................................................... [LUNs: 1]
  |     | o- lun0 ........................................ [block/BD1 (/dev/sdb) (default_tg_pt_gp)]
  |     o- portals .................................................................... [Portals: 1]
  |       o- 0.0.0.0:3260 ..................................................................... [OK]
  o- loopback ......................................................................... [Targets: 0]
</pre>

This essentially links our backstore block (BD1) device to our iqn, all grouped under our TPG.


<strong>Step 4 - create name for initiators to use</strong>: Next we need give our backstore block a unique name that initiators can reference in order to connect to it (note, we are providing the suffix string of 'client'). You can think of this initiator reference as a bit like a username, i.e. a client needs to know what this username is before hand in order to connect to it. Hence this reference is created under the:

<pre>
/> <strong>cd /iscsi/iqn.2018-02.net.cb.target:fqdn/tpg1/acls</strong>
/iscsi/iqn.20...qdn/tpg1/acls> <strong>create iqn.2018-02.net.cb.target:client</strong>
Created Node ACL for iqn.2018-02.net.cb.target:client
Created mapped LUN 0.


/iscsi/iqn.20...qdn/tpg1/acls> ls /
o- / ......................................................................................... [...]
  o- backstores .............................................................................. [...]
  | o- block .................................................................. [Storage Objects: 1]
  | | o- BD1 .............................................. [/dev/sdb (2.0GiB) write-thru activated]
  | |   o- alua ................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ....................................... [ALUA state: Active/optimized]
  | o- fileio ................................................................. [Storage Objects: 0]
  | o- pscsi .................................................................. [Storage Objects: 0]
  | o- ramdisk ................................................................ [Storage Objects: 0]
  o- iscsi ............................................................................ [Targets: 1]
  | o- iqn.2018-02.net.cb.target:fqdn .................................................... [TPGs: 1]
  |   o- tpg1 ............................................................... [no-gen-acls, no-auth]
  |     o- acls .......................................................................... [ACLs: 1]
  |     | o- iqn.2018-02.net.cb.target:client ..................................... [Mapped LUNs: 1]
  |     |   o- mapped_lun0 ................................................... [lun0 block/BD1 (rw)]
  |     o- luns .......................................................................... [LUNs: 1]
  |     | o- lun0 ........................................ [block/BD1 (/dev/sdb) (default_tg_pt_gp)]
  |     o- portals .................................................................... [Portals: 1]
  |       o- 0.0.0.0:3260 ..................................................................... [OK]
  o- loopback ......................................................................... [Targets: 0]
</pre>

At this stage, the target is now fully configured to be mounted. 


<strong>Step 5 (optional) - set username and password</strong>: We might want to also password protect this storage. this is possible by running the following command:

<pre>
/> cd /iscsi/iqn.2018-02.net.cb.target:fqdn/tpg1/acls/iqn.2018-02.net.cb.target:client/
/iscsi/iqn.20...target:client> set auth userid=codingbee
Parameter userid is now 'codingbee'.


/iscsi/iqn.20...target:client> set auth password=password
Parameter userid is now 'password'.
</pre>  

After all that's done, here is what it all finally looks like:


<pre>
/> ls
o- / ......................................................................................... [...]
  o- backstores .............................................................................. [...]
  | o- block .................................................................. [Storage Objects: 1]
  | | o- BD1 .............................................. [/dev/sdb (2.0GiB) write-thru activated]
  | |   o- alua ................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ....................................... [ALUA state: Active/optimized]
  | o- fileio ................................................................. [Storage Objects: 0]
  | o- pscsi .................................................................. [Storage Objects: 0]
  | o- ramdisk ................................................................ [Storage Objects: 0]
  o- iscsi ............................................................................ [Targets: 1]
  | o- iqn.2018-02.net.cb.target:fqdn .................................................... [TPGs: 1]
  |   o- tpg1 ............................................................... [no-gen-acls, no-auth]
  |     o- acls .......................................................................... [ACLs: 1]
  |     | o- iqn.2018-02.net.cb.target:client ..................................... [Mapped LUNs: 1]
  |     |   o- mapped_lun0 ................................................... [lun0 block/BD1 (rw)]
  |     o- luns .......................................................................... [LUNs: 1]
  |     | o- lun0 ........................................ [block/BD1 (/dev/sdb) (default_tg_pt_gp)]
  |     o- portals .................................................................... [Portals: 1]
  |       o- 0.0.0.0:3260 ..................................................................... [OK]
  o- loopback ......................................................................... [Targets: 0]
/>
</pre>

Now we can exit:

<pre>
/> exit
Global pref auto_save_on_exit=true
Last 10 configs saved in /etc/target/backup.
Configuration saved to /etc/target/saveconfig.json
</pre>

Notice the json file. This essentially stores everything we have configured using the targetcli command, which includes any usernames and passwords. It looks like this:


<pre>
[root@target ~]# cat /etc/target/saveconfig.json
{
  "fabric_modules": [],
  "storage_objects": [
    {
      "alua_tpgs": [
        {
          "alua_access_state": 0,
.
.
.
...etc
</pre>  


Next we need to whitelist the iSCSI port in firewalld:


<pre>
[root@iscsi-target ~]# firewall-cmd --permanent --add-service=iscsi-target
success
[root@iscsi-target ~]# systemctl restart firewalld
</pre>


Then we enable the target service:


<pre>
$ systemctl enable target
$ systemctl start target
</pre>




<h2>Setting up an Initiator</h2>


Next we log into our initiator box, and install the following:


<pre>
$ yum install -y iscsi-initiator-utils
</pre>

Next there are 2 files that needs to be edited, they are both in the following directory:

<pre>
[root@iscsi-initiator ~]# cd /etc/iscsi/
[root@iscsi-initiator iscsi]# ll
total 20
-rw-r--r--. 1 root root    50 Feb 26 19:44 initiatorname.iscsi
-rw-------. 1 root root 12329 Aug  7  2017 iscsid.conf
</pre> 

First the following file needs to be edited:

<pre>
[root@iscsi-initiator iscsi]# cat /etc/iscsi/initiatorname.iscsi
InitiatorName=iqn.1994-05.com.redhat:41d9a65147c3
</pre>
 
With the name we picked, so in our case this becomes:


<pre>
[root@iscsi-initiator iscsi]# cat /etc/iscsi/initiatorname.iscsi
InitiatorName=iqn.2018-05.net.codingbee.iscsi-target:clientmynewscsiblockdevice1
</pre>

Now if the target is password protected, then you need edit the <code>/etc/iscsi/iscsid.conf</code>, in particular the following section:




<pre>
.
.
.
# *************
# CHAP Settings
# *************

# To enable CHAP authentication set node.session.auth.authmethod
# to CHAP. The default is None.
<strong>#node.session.auth.authmethod = CHAP</strong>

# To set a CHAP username and password for initiator
# authentication by the target(s), uncomment the following lines:
<strong>#node.session.auth.username = username
#node.session.auth.password = password</strong>
.
.
.
</pre>

We need to uncomment these lines and add the credentials, in our example it ends up looking like this:


<pre>
# *************
# CHAP Settings
# *************

# To enable CHAP authentication set node.session.auth.authmethod
# to CHAP. The default is None.
node.session.auth.authmethod = CHAP

# To set a CHAP username and password for initiator
# authentication by the target(s), uncomment the following lines:
node.session.auth.username = codingbee
node.session.auth.password = password123

</pre>

Warning: There's another part in this file that looks similar to this segment, so need to make sure not to update the wrong part. 

Next we start the iscsi service:


<pre>
$ systemctl enable iscsi
$ systemctl start iscsi
</pre>

Now we need to scan our target server for any available iSCSI storage, we do this by using the discovery mode:

<pre>
[root@iscsi-initiator iscsi]# iscsiadm --mode discovery --type sendtargets --portal 192.168.14.100
192.168.14.100:3260,1 iqn.2018-05.net.codingbee.iscsi-target:mynewscsiblockdevice1
</pre>

Here, we specified the target server's ip address. Here we sent a request to the target server to send us all available targets. It returns one result. 


We are now ready to try to access this. But before we do, let's first run a couple of checks:

<pre>
[root@iscsi-initiator ~]# lsblk --scsi
NAME HCTL       TYPE VENDOR   MODEL             REV TRAN
sda  2:0:0:0    disk ATA      VBOX HARDDISK    1.0  sata
</pre>

Notice only sda is listed here. Also the following doesn't exist yet:

<pre>
[root@initiator iscsi]# iscsiadm --mode discovery --type sendtargets --portal 192.168.14.100
192.168.14.100:3260,1 iqn.2018-02.net.cb.target:fqdn
</pre>

Note: always use an ip address for the portal setting. Using a DNS name will cause problems. 


Now run the iscsiadm in node mode like this:


<pre>
[root@initiator iscsi]# iscsiadm --mode node --targetname iqn.2018-02.net.cb.target:fqdn --portal 192.168.14.100 --login

Logging in to [iface: default, target: iqn.2018-02.net.cb.target:fqdn, portal: 192.168.14.100,3260] (multiple)
Login to [iface: default, target: iqn.2018-02.net.cb.target:fqdn, portal: 192.168.14.100,3260] successful.
</pre>

To confirm that this has worked, run:


<pre>
[root@initiator iscsi]# lsblk --scsi
NAME HCTL       TYPE VENDOR   MODEL             REV TRAN
sda  2:0:0:0    disk ATA      VBOX HARDDISK    1.0  sata
sdb  3:0:0:0    disk LIO-ORG  BD1              4.0  iscsi

</pre>


Now you can install a filesystem on it:


<pre>
[root@initiator ~]# mkfs.xfs -L iscsi_device /dev/sdb
meta-data=/dev/sdb               isize=512    agcount=4, agsize=131072 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=0, sparse=0
data     =                       bsize=4096   blocks=524288, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0

[root@initiator ~]# blkid | grep sdb
/dev/sdb: UUID="c00cfb33-2193-4d7d-80d4-f91af3468906" TYPE="xfs"
</pre>
Note: we'll need this UUID value, see further down. 

We then create a mount point for this new filesystem:

<pre>
[root@initiator iscsi]# mkdir /mnt/remotedisk
</pre>

We can test this mounts ok by running:

<pre>
[root@initiator iscsi]# mount /dev/sdb /mnt/remotedisk

[root@initiator iscsi]# lsblk
NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
sda               8:0    0  64G  0 disk
├─sda1            8:1    0   1G  0 part /boot
└─sda2            8:2    0  63G  0 part
  ├─centos-root 253:0    0  41G  0 lvm  /
  ├─centos-swap 253:1    0   2G  0 lvm  [SWAP]
  └─centos-home 253:2    0  20G  0 lvm  /home
sdb               8:16   0   2G  0 disk /mnt/remotedisk


[root@initiator ~]# umount /dev/sdb
</pre>

We unmounted it again, so that we can now mount persistantly using the /etc/fstab appraoch. So here's the line to append:

<pre>
[root@initiator ~]# cat /etc/fstab

#
# /etc/fstab
# Created by anaconda on Fri Feb  2 05:24:03 2018
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/centos-root /                       xfs     defaults        0 0
UUID=9e33c3c1-98df-4fe1-a66f-13ffbaa5f154 /boot                   xfs     defaults        0 0
/dev/mapper/centos-home /home                   xfs     defaults        0 0
/dev/mapper/centos-swap swap                    swap    defaults        0 0
UUID=c00cfb33-2193-4d7d-80d4-f91af3468906 /mnt/remotedisk    xfs     <strong>_netdev</strong>        0 0

</pre>

Notice the '_netdev'mount option, this is prevent the boot process hanging if there is any network related problems. 

Now we test our fstab entry like this:


<pre>
[root@initiator ~]# lsblk
NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
sda               8:0    0  64G  0 disk
├─sda1            8:1    0   1G  0 part /boot
└─sda2            8:2    0  63G  0 part
  ├─centos-root 253:0    0  41G  0 lvm  /
  ├─centos-swap 253:1    0   2G  0 lvm  [SWAP]
  └─centos-home 253:2    0  20G  0 lvm  /home
sdb               8:16   0   2G  0 disk

[root@initiator ~]# mount -a

[root@initiator ~]# lsblk
NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
sda               8:0    0  64G  0 disk
├─sda1            8:1    0   1G  0 part /boot
└─sda2            8:2    0  63G  0 part
  ├─centos-root 253:0    0  41G  0 lvm  /
  ├─centos-swap 253:1    0   2G  0 lvm  [SWAP]
  └─centos-home 253:2    0  20G  0 lvm  /home
sdb               8:16   0   2G  0 disk /mnt/remotedisk
</pre>

So far so good. Finally we test our remote storage by creating a file in the new storage:


<pre>
[root@initiator remotedisk]# touch testfile.txt
[root@initiator remotedisk]# ll
total 16
drwx------. 2 root root 16384 Feb 27 11:20 lost+found
-rw-r--r--. 1 root root     0 Feb 27 11:23 testfile.txt
[root@initiator remotedisk]#
</pre>


The next step is to ensure this remote block device automounts when the machine reboots. This is done by adding a line to the /etc/fstab file. 



To get statistics about our iSCSI mount, we can run:


<pre>
[root@initiator remotedisk]# iscsiadm -m session -P 3
iSCSI Transport Class version 2.0-870
version 6.2.0.874-2
Target: iqn.2018-02.net.cb.target:fqdn (non-flash)
	Current Portal: 192.168.14.100:3260,1
	Persistent Portal: 192.168.14.100:3260,1
		**********
		Interface:
		**********
		Iface Name: default
		Iface Transport: tcp
		Iface Initiatorname: iqn.2018-02.net.cb.target:client
		Iface IPaddress: 192.168.14.101
		Iface HWaddress: <empty>
		Iface Netdev: <empty>
		SID: 1
		iSCSI Connection State: LOGGED IN
		iSCSI Session State: LOGGED_IN
		Internal iscsid Session State: NO CHANGE
		*********
		Timeouts:
		*********
		Recovery Timeout: 120
		Target Reset Timeout: 30
		LUN Reset Timeout: 30
		Abort Timeout: 15
		*****
		CHAP:
		*****
		username: codingbee
		password: ********
		username_in: <empty>
		password_in: ********
		************************
		Negotiated iSCSI params:
		************************
		HeaderDigest: None
		DataDigest: None
		MaxRecvDataSegmentLength: 262144
		MaxXmitDataSegmentLength: 262144
		FirstBurstLength: 65536
		MaxBurstLength: 262144
		ImmediateData: Yes
		InitialR2T: Yes
		MaxOutstandingR2T: 1
		************************
		Attached SCSI devices:
		************************
		Host Number: 3	State: running
		scsi3 Channel 00 Id 0 Lun: 0
			Attached scsi disk sdb		State: running
</pre>



Finally, you want to unmount this remote storage and stop the iSCSI service everytime your machine is shutdown:

<pre>
$ umount /dev/sdb
$ systemctl stop iscsi
</pre>
Also see: https://unix.stackexchange.com/questions/39226/how-to-run-a-script-with-systemd-right-before-shutdown

[post-content post_name=rhsca-quiz] 

In the quiz we'll assume we have boxes with the following names:

iSCSI server with hostname target.cb.net
iSCSI client with hostname initiator.cb.net

Server side (target.cb.net) questions:
[accordion]
[toggle title="What rpms need to be installed on the server?"]
$ yum install -y targetcli
[/toggle]
[toggle title="What is the command to enter the iSCSI interactive mode?"]
$ targetcli
[/toggle]
[toggle title="What tasks needs to be done in the interactive mode?"]
1. Register the block device  - done under /backstores/block
2. Create the iqn - done under /iscsi
3. Create a LUN (Logical Unit Number) - this associates our block device to the lun. It's done under /iscsi/{iqn-fqdn}/tpg1/luns folder
4. Create name for initiators to use - this creates a private name that initiators can connect to - it's done under /iscsi/{iqn-fqdn}/tpg1/acls
5. Set username and password (optional) - this is done under /iscsi/{iqn-fqdn}/tpg1/acls/{initiar-client-ref-name}/


[/toggle]
[toggle title="What is the command to register the /dev/sdb block device and give it the name 'BD1'?"]
cd backstores/block
create BD1 /dev/sdb
Created block storage object BD1 using /dev/sdb.
[/toggle]
[toggle title="What is the command to create the IQN, with the name suffix 'blocksdb'?"]
cd /iscsi
create iqn.YYYY-MM.net.cb.target:blocksdb
[/toggle]
[toggle title="What is the command to create the LUN?"]
cd /iscsi/{iqn-name}/tpg1/luns
create /backstores/block/BD1
[/toggle]
[toggle title="Create a name of 'client' for the intiators to use?"]
cd /iscsi/{iqn-name}/tpg1/acls
create iqn.YYYY-MM.net.cb.target:client
[/toggle]
[toggle title="What is the command to set username and password?"]
/> cd /iscsi/{iqn-name}/tpg1/acls/{initiar-client-ref-name}/
.../...:client> set auth userid=codingbee
Parameter userid is now 'codingbee'.

.../...:client>  set auth password=password
Parameter userid is now 'password'.
[/toggle]
[toggle title="What firewalld related commands need to be run?"]
[root@iscsi-target ~]# firewall-cmd --permanent --add-service=iscsi-target
success
[root@iscsi-target ~]# systemctl restart firewalld
[/toggle]
[toggle title="What is the command to start+enable the iSCSI daemon?"]
$ systemctl start target
$ systemctl enable target
[/toggle]
[/accordion]







Client side (initiator.cb.net) questions:

[accordion]
[toggle title="Whta rpms needs to be installed on the client?"]
$ yum install -y iscsi-initiator-utils
[/toggle]
[toggle title="What config files needs to be edited?"]
- /etc/iscsi/initiatorname.iscsi
- /etc/iscsi/iscsid.conf (optional)
[/toggle]
[toggle title="What configurations needs to be done on in /etc/iscsi/initiatorname.iscsi?"]
$ echo "InitiatorName=iqn.${year}-${month}.net.cb.target:client" > /etc/iscsi/initiatorname.iscsi
# i.e. you use the client reference. 
[/toggle]
[toggle title="What configurations needs to be done in /etc/iscsi/iscsid.conf?"]
No need to make any changes unless the iSCSI device is password protected. Need to update the CHAP section and enable the authmethod section, and specify username and password. 
[/toggle]
[toggle title="What is the command to search for available iSCSI storage devices on the target?"]
$ iscsiadm --mode discovery --type sendtargets --portal {target's-ip-address}
# this outputs the storage device's iSCS Qualified Name (IQN)
[/toggle]
[toggle title="What is the command to enable and start the initiator service.?"]
$ systemctl start iscsi
$ systemctl enable iscsi
[/toggle]
[toggle title="What is the command to connect to this output?"]
$ iscsiadm --mode node --targetname {iqn from previous commands output} --portal {target's-ip-address} --login
# note, this isn't needed if you started up the daemon. 
[/toggle]
[toggle title="What ways can you check this has worked assuming this device is called /dev/sdb?"]
$ lsblk --scsi
[/toggle]
[toggle title="What is the command to format /dev/sdb and give the filesystem the label 'iscsi_device'?"]
$ mkfs.xfs -L iscsi_device /dev/sdb
[/toggle]
[toggle title="What fstab entry needs to be added to automount this filesystem to mountpoint /mnt/remotedisk?"]
$ echo 'LABEL="iscsi_device" /mnt/remotedisk    xfs     _netdev        0 0' >> /etc/fstab
[/toggle]
[toggle title="What commands do need to run when shutting down the initiator?"]
$ umount /dev/sdb
$ systemctl stop iscsi
# also log out of target. see examples in man page for iscsiadm for example
[/toggle]
[/accordion]