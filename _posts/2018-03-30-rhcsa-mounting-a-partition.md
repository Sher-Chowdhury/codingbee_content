---
ID: 394
post_title: >
  How to mount a filesystem on CentOS/RHEL
  7
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-mounting-a-partition
published: true
post_date: 2018-03-30 00:00:00
---
To start storing data on a block device (e.g. a partition, logical volume, or antire storage device) you first need to install a filesystem on the block device. After that you then need to mount the filesystem. Mounting a filesystem is simply the act of associating a directory (aka a mount point) to a filesystem. Any files/folders that are then put inside this mountpoint folder will actually end up getting stored on the underlying block device that's hosting the filesystem. You can view which filesystems are attached to which mountpoint using lsblk: 

<pre>
$ lsblk
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   20G  0 disk
<strong>├─sda1            8:1    0  500M  0 part /boot
</strong>└─sda2            8:2    0 19.5G  0 part
  ├─centos-swap 253:0    0    2G  0 lvm  [SWAP]
  └─centos-root 253:1    0 17.5G  0 lvm  /
sdb               8:16   0  2.1G  0 disk
├─sdb1            8:17   0   10M  0 part
├─sdb2            8:18   0   10M  0 part
├─sdb3            8:19   0   10M  0 part
├─sdb4            8:20   0    1K  0 part
├─sdb5            8:21   0   10M  0 part
├─sdb6            8:22   0   10M  0 part
└─sdb7            8:23   0 1001M  0 part
sr0              11:0    1 55.4M  0 rom
</pre>

For example /dev/sda1 is already mounted onto "/boot". So adding any files/folders in /boot will end up getting stored in the sda1 partition, which means it will ultimately get stored on the sda storage device. 

We can also see that sdb1...sdb7 are not mounted, so we can mount them. But before we can mount them we have to first check if thy have been formatted, we can check this using blkid: 

<pre>
$ blkid
/dev/sda1: UUID="9e33c3c1-98df-4fe1-a66f-13ffbaa5f154" TYPE="xfs"
/dev/sda2: UUID="jE1A3D-X4dw-pi2u-BYxC-fvoQ-vVtn-R25igD" TYPE="LVM2_member"
/dev/mapper/centos-root: UUID="9e702351-4e25-449a-a586-9d713265a6e2" TYPE="xfs"
/dev/mapper/centos-swap: UUID="ae39ba11-dadd-498b-a279-04aaee5abe2c" TYPE="swap"
/dev/mapper/centos-home: UUID="1d84b924-8491-4e8a-9476-b969bfc5de2e" TYPE="xfs"
</pre>

If the block device you want to mount doesn't appear in this list, then you first need to format it before continuing to mount it. 


So let's try mounting sdb1. First we need to choose a mount point.. In my case, I'll choose use an existing directory, /mnt, which is currently an empty:

<pre>
$ ls -l /mnt/
total 0
</pre>

note: any content that is already in the mount-point directory will be temporarily inaccessible while it has a filesystem mounted on it. but becomes available again after you unmount the filesystem again. That’s why it is good practice to avoid using mountpoint directory for anything other than for mounting a filesystem on it.


Now to do the actual mounting, we use the "mount" command like this:


<pre>
$ mount /dev/sdb1 /mnt
</pre>
 
This command doesn't give any output if it's successful. However there are a few ways to check if this has been successful, e.g. you can check using lsblk: 


<pre>
$ lsblk
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   20G  0 disk
├─sda1            8:1    0  500M  0 part /boot
└─sda2            8:2    0 19.5G  0 part
  ├─centos-swap 253:0    0    2G  0 lvm  [SWAP]
  └─centos-root 253:1    0 17.5G  0 lvm  /
sdb               8:16   0  2.1G  0 disk
<strong>├─sdb1            8:17   0   10M  0 part /mnt       # Success</strong>
├─sdb2            8:18   0   10M  0 part
├─sdb3            8:19   0   10M  0 part
├─sdb4            8:20   0    1K  0 part
├─sdb5            8:21   0   10M  0 part
├─sdb6            8:22   0   10M  0 part
└─sdb7            8:23   0 1001M  0 part
sr0              11:0    1 55.4M  0 rom

</pre>

or you can run the mount command:

<pre>
$ mount | grep "^/dev"
/dev/mapper/centos-root on / type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
/dev/sda1 on /boot type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
<strong>/dev/sdb1 on /mnt type ext4 (rw,relatime,seclabel,data=ordered)</strong>
</pre> 


Or you can check using the df command. The df command gives an overview of filesystem diskspace usage, and consequently lists all partitions/hdds that are currently mounted:


<pre>
$ df -h
Filesystem               Size  Used Avail Use% Mounted on
/dev/mapper/centos-root   18G  4.3G   14G  25% /
devtmpfs                 488M     0  488M   0% /dev
tmpfs                    497M   80K  497M   1% /dev/shm
tmpfs                    497M   26M  472M   6% /run
tmpfs                    497M     0  497M   0% /sys/fs/cgroup
/dev/sda1                497M  143M  354M  29% /boot
<strong>/dev/sdb1                8.7M  172K  7.9M   3% /mnt</strong>
</pre>



<h3>Unmounting a filesystem</h3>

If you want to unmount sdb1, then you use the "umount" command. With this command you simply have to specify either the mountpoint or the device name for the unmount to work, i.e. you can simply run:


<pre>
$ umount /mnt
</pre>

or: 

<pre>
$ umount /dev/sdb1
</pre>


<h2>Auto mount a file system during boot time</h2>

Mounting a filesystem using the mount command isn't persistant. There are 2 ways auto mount a filesystem during boot time. There 2 ways to do this: 

<ol>
	<li>Adding an entry to the <code>/etc/fstab</code> file</li>
	<li><a href="http://codingbee.net/tutorials/rhcsa/rhcsa-automounting-using-systemd/">automounting via systemd</a></li>
</ol>


Let's take a look at the /etc/fstab approach first:


<pre>
$ cat /etc/fstab

#
# /etc/fstab
# Created by anaconda on Sat Mar 14 19:14:11 2015
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/centos-root /                       xfs     defaults        1 1
UUID=547c78ce-34b5-4e31-84d2-bf72b196fa57 /boot                   xfs     defaults        1 2
/dev/mapper/centos-swap swap                    swap    defaults        0 0
</pre>

To automount your filesystem you need to add a new entry in this file. Each entry is made up of 6 fields. These fields are described in the in man page:



<pre>$ man fstab</pre>

Each row is made up of column entries:


<strong>Column 1</strong> - This can be the block's UUID=... or LABEL=... value, or a logical volume name, /dev/mapper/... . Notice that we don't specify block devices, e.g. /dev/sda1. That's because occasionally it is possible that a block device actually gets mapped to a different hdd/partition if machine is rebooted. That's why block devices can be ambiguous.  

<strong>Column 2</strong> - This is the mountpoint

<strong>Column 3</strong> - This is the file system type, e.g. ext4, xfs,...etc. 

<strong>Column 4</strong> - This is where you can specify various mount options. It can be set to "defaults", or a comma seperated list. The fstab man page and the mount page lists all the mount options that are independent of any file system types. Whereas file system type mount options are detailed in their respective file system type man pages. In the man pages you'll discover that the "defaults" covers the following:


<pre>
.
.
defaults
              Use default options: rw, suid, dev, exec, auto, nouser, and async.
.
.
</pre>   

These are the defaults for non file system specific default mount options. For file system specific default options, you will need to check out the respective mount options. 

<strong>Column 5</strong> - This is to do with backup support for the <a href="http://en.wikipedia.org/wiki/Dump_(program)">dump backup utility</a>. This is not used that often anymore but if you do want to your filesystem to be backed up then you should enable this by setting it to "1". It is recommended to set this to "1". If however you are dealing with a swap partition, then you should leave this disabled by setting it to "0".

<strong>Column 6</strong> - relates to the ordering that fsck performs all it check through all the devices. the root directory (/) should be set to 1, and all other filesystems should be set to 2. This is so that the root filesystem has a higher priority over all other file systems. You should also disable this check (by setting it to 0) for readonly filesystems, or swap partitions. In all other cases, it is recommended that this is set to "2".


Tip: use the info from blkid to create your line.

Also when it comes to column 5 and 6, these are usually set to '1 2'. However for swaps they are set to '0 0'

For more info about these 6 column's check out:

<pre>$ man fstab</pre>

This man page is actually really handy. 
  


Once you have added your lines in the fstab, you can then test that your new configuration works by running the following:


<pre>
$ mount -a
</pre>


This will mount all block devices that are specified in the /etfc/fstab, unless they are already mounted. If all goes well then the above command won't output anything. You can confirm if it has been successful by running lsblk/mount/df commands as described earlier. 


<strong>Warning:</strong> it is important to test your /etc/fstab entry because any errors in it will stop your machine from rebooting. 

[post-content post_name=rhsca-quiz] 

[accordion]
[toggle title="What is the command to find more info about what all the /etc/fstab columns mean?"]
$ man fstab
[/toggle]
[toggle title="What is the command to find more about what the default mount options are?"]
$ man fstab
[/toggle]
[toggle title="What is the command to manually mount '/dev/sdb1' to /mnt?"]
$ mount /dev/sdb1 /mnt
[/toggle]
[toggle title="What are the three command to indicate whether a mounting has been successful?"]
$ lsblk
# or
$ mount
# or
$ df -h
[/toggle]
[toggle title="What are the 2 commands to manually unmount '/dev/sdb1' from /mnt?"]
$ umount /mnt
# or
$ umount /dev/sdb1
[/toggle]
[toggle title="Where can you find info about the avaialable mount options for xfs?"]
$ man xfs
[/toggle]
[toggle title="What are the 2 ways to automount a filesystem during machine boot up?"]
- automounting via systemd
- Adding an entry to the /etc/fstab file
[/toggle]
[toggle title="What are each field in the fstab file mean?"]
column 1 - UUID=... or LABEL=... or /dev/mapper/
column 2 - mountpoint
column 3 - filesystem type
column 4 - comma seperated mount options
column 5 - backup support - normally 1, but zero for swaps
column 6 - fsck (file system check) - normally 2, but 0 for swaps
[/toggle]
[toggle title="What are column 5 and 6 in /etc/fstab normally set to?"]
'0 0' for swaps, cifs, and nfs. And '1 2' for everything else. 
[/toggle]
[toggle title="What is the command to check that the entry you added in your fstab is valid?"]
$ mount -a
[/toggle]
[/accordion]