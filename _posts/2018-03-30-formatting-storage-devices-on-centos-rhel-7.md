---
ID: 393
post_title: >
  Formatting storage devices on
  CentOS/RHEL 7
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/formatting-storage-devices-on-centos-rhel-7
published: true
post_date: 2018-03-30 00:00:00
---
A storage device is a term used loosely to mean anything that's used for storing data. Storage devices comes in various forms, but they most commonly in the form of a partition (e.g. /dev/sdb2), or an unpartitioned block device (e.g. /dev/sdc), or a logical volume (e.g. /dev/mapper/centos-home). Before you can start storing data in storage device, you first need to: 

<ol>
	<li><strong>Format the storage device</strong> - Aka install a filesystem.</li>
	<li><strong>Mount the filesystem</strong> - Attach the filesystem to a folder on your system</li>
</ol>


<h2>File system types</h2>
There are several main filesystem types that are available:


<ul>
	<li><strong>XFS</strong> - This is now the default filesystem in RHEL 7.</li>
	<li><strong>Ext4</strong> - This used to be the default file system up to and including RHEL 6</li>
	<li><strong>vfat</strong> - This is typically used for removable media, e.g. USB pens</li>

</ul>



To view a complete list of supported filesystems, check out:


<pre>
$ man fs
</pre>

Each file system type has their own man page as well, e.g. to learn more about xfs, you do:


<pre>$ man xfs</pre>

This is a bit like a library that doesn't have any bookshelves, and hence unable to store any books. Installing a filesystem is a bit like filling the library up with book shelves. 


<h2>Install a file system</h2>
To install a filesystem on a partition (or an unpartitioned hdd) you need to use one of the mkfs.* commands:


<pre>
$ mkfs   # I pressed the tab key twice, write after typing "mkfs"
mkfs         mkfs.cramfs  mkfs.ext3    mkfs.fat     mkfs.msdos   mkfs.xfs
mkfs.btrfs   mkfs.ext2    mkfs.ext4    mkfs.minix   mkfs.vfat
</pre> 



Let's now install the xfs filesystem on a partition:

<pre>
$ mkfs.xfs -L "DummyStorage" /dev/sdb7
meta-data=/dev/sdb7              isize=256    agcount=4, agsize=64064 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=0        finobt=0
data     =                       bsize=4096   blocks=256256, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=0
log      =internal log           bsize=4096   blocks=853, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0

</pre>

Notice that I used the "L" switch. This is optional, but is recommended because it lets you assign a human friendly name to your partition/hdd. This is important because the current name, (which in this case is /dev/sdb7) was a name that was auto-assigned to do the device, and if you then plug in a usb-pen, then reboot your machine, then there is a chance that the usb-pen will change which devices are auto-assigned which name. Hence labels solve this problem.  

Having a label or knowing the UUID (which we will cover next) is important, since we need this info as part of the 3rd and final step, which is mounting the partition. 


As soon as a hdd/partition is formatted, a unique ID (UUID) is assigned to that block device. You can use the blkid command to view a list of all formatted block devices along with their UUID value:

<pre>
$ blkid
/dev/sda1: UUID="547c78ce-34b5-4e31-84d2-bf72b196fa57" TYPE="xfs"
/dev/sda2: UUID="JnfbeO-XPAG-AS0O-Mrtw-hnwn-7hqf-Mhk46t" TYPE="LVM2_member"
/dev/sdb1: LABEL="DummyExt4Storage" UUID="eeb45452-3e56-4455-a7c0-0e2e41d8a3cf" TYPE="ext4"
/dev/sdb7: LABEL="DummyStorage" UUID="f252bb11-abaa-4744-8001-e82b0e066b17" TYPE="xfs"
/dev/mapper/centos-swap: UUID="94263d84-5357-4566-9987-fc74ca946a96" TYPE="swap"
/dev/mapper/centos-root: UUID="17f09dae-88d1-4813-9738-8996819cf6ba" TYPE="xfs"
</pre>

Any unformatted block devices (e.g. /dev/sdb2) won't appear in the above list. The blkid command only displays formatted block devices, along with UUID, Label (if any), and the type of filesystem installed. All of this info is useful when adding entries to the <code>/etc/fstab</code> file. 



<h2>Using the "file" command to analyse block devices</h2>

If you have a hdd/partition and want to find out which file system type is installed on it, then the best way to do this is by using the "file" command with the (s)pecial file option enabled so that it does additional analysis of it's target. I also enabled the symbolic (L)ink option, so to auto-resolve any sybmolic links. Here are a few examples:

<pre>
$ file -sL /dev/sda1
/dev/sda1: SGI XFS filesystem data (blksz 4096, inosz 256, v2 dirs)
</pre>

Next we have:

<pre>
$ file -sL /dev/sdb2
/dev/sdb2: data
</pre>

Here it means that sdb2 is not formatted. Here's an example of a partition with an ext4 filesystem installed on it:
<pre>
$ file -sL /dev/sdb1
/dev/sdb1: Linux rev 1.0 ext4 filesystem data, UUID=eeb45452-3e56-4455-a7c0-0e2e41d8a3cf, volume name "DummyExt4Storage" (extents) (64bit) (huge files)
</pre>

Next we have an example of an LVM's physical volume:

<pre>
$ file -sL /dev/sda2
/dev/sda2: LVM2 PV (Linux Logical Volume Manager), UUID: JnfbeO-XPAG-AS0O-Mrtw-hnwn-7hqf-Mhk46t, size: 20949499904
</pre>
 

<h2>Delete a file system</h2>

You might think that deleting a file system could be done by deleting entire partition (and recreate it again) using fdisk. However that doesn't work since deleting a partition doesn't delete any data on the disk, it just deletes the artificial partition boundaries. In fact deleting a file system is best done with the help of the <a href="http://en.wikipedia.org/?title=/dev/zero">/dev/zero</a> file:


<pre>
$ cat /dev/zero > /dev/sdb1
</pre>

Here's the approach in action:


<pre>
$ lsblk
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   20G  0 disk
├─sda1            8:1    0  500M  0 part /boot
└─sda2            8:2    0 19.5G  0 part
  ├─centos-swap 253:0    0    2G  0 lvm  [SWAP]
  └─centos-root 253:1    0 17.5G  0 lvm  /
sdb               8:16   0  2.1G  0 disk
└─sdb1            8:17   0  200M  0 part
sr0              11:0    1 55.4M  0 rom


$ blkid | grep sdb1
/dev/sdb1: UUID="9fa9e491-8e21-4322-a1c2-dc269825e824" TYPE="xfs"


$ cat /dev/zero > /dev/sdb1
cat: write error: No space left on device


$ lsblk
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   20G  0 disk
├─sda1            8:1    0  500M  0 part /boot
└─sda2            8:2    0 19.5G  0 part
  ├─centos-swap 253:0    0    2G  0 lvm  [SWAP]
  └─centos-root 253:1    0 17.5G  0 lvm  /
sdb               8:16   0  2.1G  0 disk
└─sdb1            8:17   0  200M  0 part
sr0              11:0    1 55.4M  0 rom
$ blkid | grep sdb1

</pre>

As you can see the filesystem has been deleted but the partition itself has remained intact. 




[post-content post_name=rhsca-quiz] 

[accordion]
[toggle title="Where can you find info about what mount options are available for each time of filesystem?"]
$ man mount
[/toggle]
[toggle title="What is the command to view info about what filesystem types are supported in your machine?"]
$ man fs
[/toggle]
[toggle title="What is the command to view help info about the 'xfs' filesystem?"]
$ man xfs
[/toggle]
[toggle title="How do you list all the available formatting commands?"]
$ mkfs  # then type tab+tab
[/toggle]
[toggle title="What is the command to install the xfs filesystem onto '/dev/sdb7', and then label this filesystem with the name 'DummyStorage'?"]
$ mkfs.xfs -L "DummyStorage" /dev/sdb7
[/toggle]
[toggle title="What is the command to view all your formatted block device's filesystem type, UUID, as well as corresponding label (if any)?"]
$ blkid
[/toggle]
[toggle title="What is the above command actually displaying?"]
It displays filesystems rather than devices or partitions. If a device/partition does not have filesystem on it, then it doesn't appear in the list. 
[/toggle]
[toggle title="What is the command assign the label 'storage' to an existing xfs filesystem that is installed on /dev/sdb7?"]
$ xfs_admin -L "storage" /dev/sdb7
[/toggle]
[toggle title="What is the command assign the label 'webdata' to an existing ext4 filesystem that is installed on /dev/sdb1?"]
$ e2label /dev/sdb1 webdata
[/toggle]
[toggle title="What is the command to delete the filesystem that is installed on /dev/sdb1?"]
$ cat /dev/zero > /dev/sdb1
[/toggle]
[/accordion]