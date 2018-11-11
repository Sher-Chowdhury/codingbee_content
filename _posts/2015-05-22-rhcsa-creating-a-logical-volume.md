---
ID: 398
post_title: 'RHCSA &#8211; Creating a Logical Volume'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-creating-a-logical-volume
published: true
post_date: 2015-05-22 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is the command to create a partition from /dev/sdb?"]
$ fdisk /dev/sdb
[/toggle]
[toggle title="What is the command to create a PV from /dev/sdb1?"]
$ pvcreate /dev/sdb1
[/toggle]
[toggle title="What is the command to create a VG with the name <em>vgdata1vg</em> from the PV,  /dev/sdb1?"]
$ vgcreate vgdata1vg /dev/sdb1
[/toggle]
[toggle title="What is the command to create a 300MB LV (called <em>lvmyfirstlv</em>) from the VG, <em>vgdata1vg</em>?"]
$ lvcreate -n lvmyfirstlv -L 300M vgdata1vg
[/toggle]
[toggle title="What are the 2 symbolic links that are created under the /dev directory after running the above command?"]
/dev/mapper/vgdata1vg-lvmyfirstlv   # i.e. /dev/mapper/{vgname}-{lvname}
# and:
/dev/vgdata1vg/lvmyfirstlv   # i.e. /dev/{vg-name}/{lv-name}
[/toggle]
[toggle title="What is the command to install the ext4 filesystem on LV, lvmyfirstlv, which was created from the VG, vgdata1vg?"]
$ mkfs.ext4 /dev/vgdata1vg/lvmyfirstlv
[/toggle]
[toggle title="What is the command for creating the mountpoint, /tmp/lvdata1?"]
$ mkdir /tmp/lvdata1
[/toggle]
[toggle title="What is the command for manually mounting the LV, /dev/vgdata1vg/lvmyfirstlv onto the mountpoint /tmp/lvdata1?"]
$ mount /dev/vgdata1vg/lvmyfirstlv /tmp/lvdata1
[/toggle]
[toggle title="What is the entry you need to add to the /etc/fstab file to automount this logical volumen at boot time?"]
/dev/vgdata1vg/lvmyfirstlv   /tmp/lvdata1 ext4 defaults 0 0
[/toggle]
[toggle title="What is the command to check that the above entry is correct?"]
$ mount -a
[/toggle]
[toggle title="What are the 3 commands to check that filesystem has been mounted?"]
$ df -h    # this lists all active filesystems
# or
$ lsblk
# or 
$ mount
[/toggle]

[/accordion]

<hr/>







To understand how to create a Logical Volume, it's best to walk through a scenario:

<strong>Scenario</strong>
In this scenario we have attached a new 2GB hdd to our machine (/dev/sdb), and we now want to do the following:

<ol>
	<li>We want to create 3 partitions on this hdd, of which the second and third partitions are going to be used as part of LVM. The 1st partition will be 1GB in size, whereas the 2nd and 3rd partition will be 500MB each. </li>
	<li>Create Physical Volumes out of partitions 2 and 3. 
 	<li>We will create a new volume group, which will comprise of the 2nd and 3rd partitions. </li>
	<li>We will then create 2 logical volumes from this volume group.</li>
	<li>The first lv will have the xfs filesystem installed on it. And the second will have the ext4 filesystem installed on it. </li>
	<li>These logical volume then needs to be mounted and be available for use</li>
</ol>

<h2>Create partitions to be used for LVM</h2>
Right now we just have the 2GB hdd with no partitions:

<pre>
$ lsblk
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   20G  0 disk
├─sda1            8:1    0  500M  0 part /boot
└─sda2            8:2    0 19.5G  0 part
  ├─centos-swap 253:0    0    2G  0 lvm  [SWAP]
  └─centos-root 253:1    0 17.5G  0 lvm  /
<mark>sdb               8:16   0  2.1G  0 disk</mark>
sr0              11:0    1 55.4M  0 rom
</pre>
   
Now let's use fdisk to create the 3 partitions:

<pre>
$ fdisk /dev/sdb
Welcome to fdisk (util-linux 2.23.2).

Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p):
Using default response p
Partition number (1-4, default 1):
First sector (2048-4310167, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-4310167, default 4310167): +1100M
Partition 1 of type Linux and of size 1.1 GiB is set

Command (m for help): n
Partition type:
   p   primary (1 primary, 0 extended, 3 free)
   e   extended
Select (default p): p
Partition number (2-4, default 2):
First sector (2254848-4310167, default 2254848):
Using default value 2254848
Last sector, +sectors or +size{K,M,G} (2254848-4310167, default 4310167): +500M
Partition 2 of type Linux and of size 500 MiB is set

Command (m for help): n
Partition type:
   p   primary (2 primary, 0 extended, 2 free)
   e   extended
Select (default p): p
Partition number (3,4, default 3):
First sector (3278848-4310167, default 3278848):
Using default value 3278848
Last sector, +sectors or +size{K,M,G} (3278848-4310167, default 4310167):
Using default value 4310167
Partition 3 of type Linux and of size 503.6 MiB is set

Command (m for help): p

Disk /dev/sdb: 2206 MB, 2206806016 bytes, 4310168 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0xf1258b4b

   Device Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048     2254847     1126400   83  Linux
/dev/sdb2         2254848     3278847      <mark>512000   83  Linux</mark>
/dev/sdb3         3278848     4310167      <mark>515660   83  Linux</mark>

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
$ lsblk
NAME            MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda               8:0    0    20G  0 disk
├─sda1            8:1    0   500M  0 part /boot
└─sda2            8:2    0  19.5G  0 part
  ├─centos-swap 253:0    0     2G  0 lvm  [SWAP]
  └─centos-root 253:1    0  17.5G  0 lvm  /
sdb               8:16   0   2.1G  0 disk
├─sdb1            8:17   0   1.1G  0 part
├─sdb2            8:18   0   500M  0 part
└─sdb3            8:19   0 503.6M  0 part
sr0              11:0    1  55.4M  0 rom

</pre>
 
As you can see we have now created 3 partitions. However partitions 2 and 3 are currently listed as standard Linux partitions (type 83) as highlighted above. Therefore we need to switch this to the LVM partition type:


<pre>
$ fdisk /dev/sdb
Welcome to fdisk (util-linux 2.23.2).

Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


Command (m for help): m
Command action
   a   toggle a bootable flag
   b   edit bsd disklabel
   c   toggle the dos compatibility flag
   d   delete a partition
   g   create a new empty GPT partition table
   G   create an IRIX (SGI) partition table
   l   list known partition types
   m   print this menu
   n   add a new partition
   o   create a new empty DOS partition table
   p   print the partition table
   q   quit without saving changes
   s   create a new empty Sun disklabel
   t   change a partition's system id
   u   change display/entry units
   v   verify the partition table
   w   write table to disk and exit
   x   extra functionality (experts only)

Command (m for help): t
Partition number (1-3, default 3): 2
Hex code (type L to list all codes): L

 0  Empty           24  NEC DOS         81  Minix / old Lin bf  Solaris
 1  FAT12           27  Hidden NTFS Win 82  Linux swap / So c1  DRDOS/sec (FAT-
 2  XENIX root      39  Plan 9          83  Linux           c4  DRDOS/sec (FAT-
 3  XENIX usr       3c  PartitionMagic  84  OS/2 hidden C:  c6  DRDOS/sec (FAT-
 4  FAT16 <32M      40  Venix 80286     85  Linux extended  c7  Syrinx
 5  Extended        41  PPC PReP Boot   86  NTFS volume set da  Non-FS data
 6  FAT16           42  SFS             87  NTFS volume set db  CP/M / CTOS / .
 7  HPFS/NTFS/exFAT 4d  QNX4.x          88  Linux plaintext de  Dell Utility
 8  AIX             4e  QNX4.x 2nd part <mark>8e  Linux LVM</mark>       df  BootIt
 9  AIX bootable    4f  QNX4.x 3rd part 93  Amoeba          e1  DOS access
 a  OS/2 Boot Manag 50  OnTrack DM      94  Amoeba BBT      e3  DOS R/O
 b  W95 FAT32       51  OnTrack DM6 Aux 9f  BSD/OS          e4  SpeedStor
 c  W95 FAT32 (LBA) 52  CP/M            a0  IBM Thinkpad hi eb  BeOS fs
 e  W95 FAT16 (LBA) 53  OnTrack DM6 Aux a5  FreeBSD         ee  GPT
 f  W95 Ext'd (LBA) 54  OnTrackDM6      a6  OpenBSD         ef  EFI (FAT-12/16/
10  OPUS            55  EZ-Drive        a7  NeXTSTEP        f0  Linux/PA-RISC b
11  Hidden FAT12    56  Golden Bow      a8  Darwin UFS      f1  SpeedStor
12  Compaq diagnost 5c  Priam Edisk     a9  NetBSD          f4  SpeedStor
14  Hidden FAT16 <3 61  SpeedStor       ab  Darwin boot     f2  DOS secondary
16  Hidden FAT16    63  GNU HURD or Sys af  HFS / HFS+      fb  VMware VMFS
17  Hidden HPFS/NTF 64  Novell Netware  b7  BSDI fs         fc  VMware VMKCORE
18  AST SmartSleep  65  Novell Netware  b8  BSDI swap       fd  Linux raid auto
1b  Hidden W95 FAT3 70  DiskSecure Mult bb  Boot Wizard hid fe  LANstep
1c  Hidden W95 FAT3 75  PC/IX           be  Solaris boot    ff  BBT
1e  Hidden W95 FAT1 80  Old Minix
Hex code (type L to list all codes): 8e
Changed type of partition 'Linux' to 'Linux LVM'

Command (m for help): t
Partition number (1-3, default 3):
Hex code (type L to list all codes): 8e
Changed type of partition 'Linux' to 'Linux LVM'

Command (m for help): p

Disk /dev/sdb: 2206 MB, 2206806016 bytes, 4310168 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0xf1258b4b

   Device Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048     2254847     1126400   83  Linux
/dev/sdb2         2254848     3278847      512000   <strong>8e  Linux LVM</strong>
/dev/sdb3         3278848     4310167      515660   <strong>8e  Linux LVM</strong>

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.

</pre>  


We have now created the 3 partitions and allocated partions 2 and 3 for LVM usage. This now means that we can create physical volumes out of partitions 2 and 3. 


<h2>Create physical volumes</h2>

There are a few commands available for doing pv related tasks:


<pre>
$ pv
pvchange   pvck       pvcreate   pvdisplay  pvmove     pvremove   pvresize   pvs        pvscan
</pre>

Let's list all the pvs that currently exists:

<pre>
$ pvs
  PV         VG     Fmt  Attr PSize  PFree
  /dev/sda2  centos lvm2 a--  19.51g    0
</pre>

Note: The "pvdislay" command also shows pretty much the same info.


As you can see, /dev/sdb2 and /dev/sdb3 are not listed here. To create PVs, we use the pvcreate command:


<pre>
$ pvcreate --help
  pvcreate: Initialize physical volume(s) for use by LVM

pvcreate
        [--norestorefile]
        [--restorefile file]
        [--commandprofile ProfileName]
        [-d|--debug]
        [-f[f]|--force [--force]]
        [-h|-?|--help]
        [--labelsector sector]
        [-M|--metadatatype 1|2]
        [--pvmetadatacopies #copies]
        [--bootloaderareasize BootLoaderAreaSize[bBsSkKmMgGtTpPeE]]
        [--metadatasize MetadataSize[bBsSkKmMgGtTpPeE]]
        [--dataalignment Alignment[bBsSkKmMgGtTpPeE]]
        [--dataalignmentoffset AlignmentOffset[bBsSkKmMgGtTpPeE]]
        [--setphysicalvolumesize PhysicalVolumeSize[bBsSkKmMgGtTpPeE]
        [-t|--test]
        [-u|--uuid uuid]
        [-v|--verbose]
        [-y|--yes]
        [-Z|--zero {y|n}]
        [--version]
        PhysicalVolume [PhysicalVolume...]
</pre>


Let's now initialize the 2nd and 3rd partitions into physical volumes now:


<pre>
$ pvcreate /dev/sdb2
  Physical volume "/dev/sdb2" successfully created
$ pvcreate /dev/sdb3
  Physical volume "/dev/sdb3" successfully created
$ pvs
  PV         VG     Fmt  Attr PSize   PFree
  /dev/sda2  centos lvm2 a--   19.51g      0
  /dev/sdb2         lvm2 ---  500.00m 500.00m
  /dev/sdb3         lvm2 ---  503.57m 503.57m
</pre>

Now we have 2 physical volumes, but they haven't been added to a VG yet, hence the empty fields under the VG column. 



<h2>Create volume group</h2>

There are a few commands available for doing vg related tasks:

<pre>
$ vg
vgcfgbackup    vgck           vgdisplay      vgimport       vgmknodes      vgrename       vgsplit
vgcfgrestore   vgconvert      vgexport       vgimportclone  vgreduce       vgs
vgchange       vgcreate       vgextend       vgmerge        vgremove       vgscan
</pre>

Let's see what vg currently exists using the vgs command:


<pre>
$ vgs
  VG     #PV #LV #SN Attr   VSize  VFree
  centos   1   2   0 wz--n- 19.51g    0
</pre>


Now to create a new vg, we use the vgcreate command:


<pre>
 vgcreate --help
  vgcreate: Create a volume group

vgcreate
        [-A|--autobackup {y|n}]
        [--addtag Tag]
        [--alloc AllocationPolicy]
        [-c|--clustered {y|n}]
        [--commandprofile ProfileName]
        [-d|--debug]
        [-h|--help]
        [-l|--maxlogicalvolumes MaxLogicalVolumes]
        [--metadataprofile ProfileName]
        [-M|--metadatatype 1|2]
        [--[vg]metadatacopies #copies]
        [-p|--maxphysicalvolumes MaxPhysicalVolumes]
        [-s|--physicalextentsize PhysicalExtentSize[bBsSkKmMgGtTpPeE]]
        [-t|--test]
        [-v|--verbose]
        [--version]
        [-y|--yes]
        [ PHYSICAL DEVICE OPTIONS ]
        VolumeGroupName PhysicalDevicePath [PhysicalDevicePath...]

</pre>


Let's now create our new vg, "vgdata1vg", but for demonstration purposes, we'll only add one physical volume initially:


<pre>
$ vgcreate vgdata1vg /dev/sdb2
  Volume group "vgdata1vg" successfully created
</pre>

Let's now confirm that our vg now exists:

<pre>
$ vgs
  VG        #PV #LV #SN Attr   VSize   VFree
  centos      1   2   0 wz--n-  19.51g      0
  vgdata1vg   1   0   0 wz--n- 496.00m 496.00m
</pre>

Notice here that it gives info that our vg is only made up of 1 PV so far, and that no LV have been created from vgdata1vg. The vgs command is really useful because it gives you a nice overall LVM status. 

Now that our vg exists, to add the other pv to our vg, we need to use the vgextend command:


<pre>
$  vgextend --help
  vgextend: Add physical volumes to a volume group

vgextend
        [-A|--autobackup y|n]
        [--restoremissing]
        [--commandprofile ProfileName]
        [-d|--debug]
        [-f|--force]
        [-h|--help]
        [-t|--test]
        [-v|--verbose]
        [--version]
        [-y|--yes]
        [ PHYSICAL DEVICE OPTIONS ]
        VolumeGroupName PhysicalDevicePath [PhysicalDevicePath...]
</pre>
 

Therfore let's add sdb3 to vgdata1vg right now:

<pre>
$ vgextend vgdata1vg /dev/sdb3
  Volume group "vgdata1vg" successfully extended
$ vgs
  VG        #PV #LV #SN Attr   VSize   VFree
  centos      1   2   0 wz--n-  19.51g      0
  vgdata1vg   2   0   0 wz--n- 996.00m 996.00m
</pre>

This has now worked. Notice that total disk space is about 4MB less than the 1GB that was expected. That's becuase that 4MB is used by LVM internally for metadata purposes. 


If we now run the pvs command now, we'll see that both PVs are now attached to our new VG:


<pre>
$ pvs
  PV         VG        Fmt  Attr PSize   PFree
  /dev/sda2  centos    lvm2 a--   19.51g      0
  /dev/sdb2  vgdata1vg lvm2 a--  496.00m 496.00m
  /dev/sdb3  vgdata1vg lvm2 a--  500.00m 500.00m
</pre>



<h2>Create Logical Volumes</h2>


There are a few commands available for doing lv related tasks:

<pre>
$ lv
lvchange     lvdisplay    lvmchange    lvmdump      lvmsar       lvrename     lvscan
lvconvert    lvextend     lvmconf      lvmetad      lvreduce     lvresize
lvcreate     lvm          lvmdiskscan  lvmsadc      lvremove     lvs
</pre>




Let's see what LVs currently exists using the lvs command:


<pre>
$ lvs
  LV   VG     Attr       LSize  Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  root centos -wi-ao---- 17.51g
  swap centos -wi-ao----  2.00g

</pre>

These LVs are also represented in the form of files, under /dev/mapper:


<pre>
$ ls -l /dev/mapper/
total 0
lrwxrwxrwx. 1 root root       7 May 22 18:39 centos-root -> ../dm-1
lrwxrwxrwx. 1 root root       7 May 22 18:39 centos-swap -> ../dm-0
crw-------. 1 root root 10, 236 May 22 18:39 control

</pre>

As you can see the naming convention of these files are "vgname-lvname"


Now to create LVs, we need to use the lvcreate command:


<pre>
$ lvcreate --help
  lvcreate: Create a logical volume

lvcreate
        [-A|--autobackup {y|n}]
        [-a|--activate [a|e|l]{y|n}]
        [--addtag Tag]
        [--alloc AllocationPolicy]
        [-H|--cache
          [--cachemode {writeback|writethrough}]
        [--cachepool CachePoolLogicalVolume{Name|Path}]
        [-c|--chunksize ChunkSize]
        [-C|--contiguous {y|n}]
        [--commandprofile ProfileName]
        [-d|--debug]
        [-h|-?|--help]
        [--errorwhenfull {y|n}]
        [--ignoremonitoring]
        [--monitor {y|n}]
        [-i|--stripes Stripes [-I|--stripesize StripeSize]]
        [-k|--setactivationskip {y|n}]
        [-K|--ignoreactivationskip]
        {-l|--extents LogicalExtentsNumber[%{VG|PVS|FREE}] |
         <strong>-L|--size LogicalVolumeSize[bBsSkKmMgGtTpPeE]}</strong>
        [-M|--persistent {y|n}] [-j|--major major] [--minor minor]
        [--metadataprofile ProfileName]
        [-m|--mirrors Mirrors [--nosync]
          [{--mirrorlog {disk|core|mirrored}|--corelog}]]
        <strong>[-n|--name LogicalVolumeName]</strong>
        [--noudevsync]
        [-p|--permission {r|rw}]
        [--poolmetadatasize MetadataSize[bBsSkKmMgG]]
        [--poolmetadataspare {y|n}]]
        [--[raid]minrecoveryrate Rate]
        [--[raid]maxrecoveryrate Rate]
        [-r|--readahead {ReadAheadSectors|auto|none}]
        [-R|--regionsize MirrorLogRegionSize]
        [-T|--thin
          [--discards {ignore|nopassdown|passdown}]
        [--thinpool ThinPoolLogicalVolume{Name|Path}]
        [-t|--test]
        [--type VolumeType]
        [-v|--verbose]
        [-W|--wipesignatures {y|n}]
        [-Z|--zero {y|n}]
        [--version]
        <strong>VolumeGroupName [PhysicalVolumePath...]</strong>
.
.
.
</pre>


The important options are highlighted above. Based on the help info, let's now create our new LVs:

<pre>
$ lvcreate -n lvmyfirstlv -L 300M vgdata1vg
  Logical volume "lvmyfirstlv" created.
$ lvcreate -n lvmysecondlv -L 400M vgdata1vg
  Logical volume "lvmysecondlv" created.

</pre>


Let's now confirm that this has worked:


<pre>
$ lvs
  LV           VG        Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  root         centos    -wi-ao----  17.51g
  swap         centos    -wi-ao----   2.00g
  lvmyfirstlv  vgdata1vg -wi-a----- 300.00m
  lvmysecondlv vgdata1vg -wi-a----- 400.00m
</pre>

And also:

<pre>
$ vgs
  VG        #PV #LV #SN Attr   VSize   VFree
  centos      1   2   0 wz--n-  19.51g      0
  vgdata1vg   2   2   0 wz--n- 996.00m 296.00m
</pre>


Also the following files have been created:


<pre>
$ ls -l /dev/mapper/
total 0
lrwxrwxrwx. 1 root root       7 May 22 18:39 centos-root -> ../dm-1
lrwxrwxrwx. 1 root root       7 May 22 18:39 centos-swap -> ../dm-0
crw-------. 1 root root 10, 236 May 22 18:39 control
<strong>lrwxrwxrwx. 1 root root       7 May 22 22:30 vgdata1vg-lvmyfirstlv -> ../dm-2
lrwxrwxrwx. 1 root root       7 May 22 22:31 vgdata1vg-lvmysecondlv -> ../dm-3
</strong>$ ls -l /dev/ | egrep "dm-2|dm-3"
<strong>brw-rw----. 1 root disk     253,   2 May 22 22:30 dm-2
brw-rw----. 1 root disk     253,   3 May 22 22:31 dm-3
</strong>

</pre>

We also have the following symbolic links that points to the same block devices as well:


<pre>

$ ls -l /dev | grep vgdata1vg
drwxr-xr-x. 2 root root           80 May 22 22:31 vgdata1vg
$ ls -l /dev/vgdata1vg
total 0
lrwxrwxrwx. 1 root root 7 May 22 22:30 lvmyfirstlv -> ../dm-2
lrwxrwxrwx. 1 root root 7 May 22 22:31 lvmysecondlv -> ../dm-3

</pre>


As you can see we have 2 sets of symbolic links pointing to the same block files. The naming convention this time is:

<pre>/dev/{vg-name}/{lv-name}</pre>

These symbolic links are designed to make our lives easier when we want to refer to a particular LV. These symbolic links becomes useful when we come to installing filesystems and mounting our LVs. 

It doesn't matter which symbolic links you use, as long as you are comfortable with them. 


<h2>Install file system onto logical volumes</h2>

Just before we start installing file system's let's take a look at the current status:



<pre>
lsblk
NAME                       MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda                          8:0    0    20G  0 disk
├─sda1                       8:1    0   500M  0 part /boot
└─sda2                       8:2    0  19.5G  0 part
  ├─centos-swap            253:0    0     2G  0 lvm  [SWAP]
  └─centos-root            253:1    0  17.5G  0 lvm  /
sdb                          8:16   0   2.1G  0 disk
├─sdb1                       8:17   0   1.1G  0 part
├─sdb2                       8:18   0   500M  0 part
│ └─vgdata1vg-lvmyfirstlv  253:2    0   300M  0 lvm
└─sdb3                       8:19   0 503.6M  0 part
  └─vgdata1vg-lvmysecondlv 253:3    0   400M  0 lvm
sr0                         11:0    1  55.4M  0 rom
[root@localhost /]# blkid
/dev/sr0: UUID="2015-03-02-14-24-20-00" LABEL="VBOXADDITIONS_4.3.24_98716" TYPE="iso9660"
/dev/sda1: UUID="547c78ce-34b5-4e31-84d2-bf72b196fa57" TYPE="xfs"
/dev/sda2: UUID="JnfbeO-XPAG-AS0O-Mrtw-hnwn-7hqf-Mhk46t" TYPE="LVM2_member"
/dev/mapper/centos-swap: UUID="94263d84-5357-4566-9987-fc74ca946a96" TYPE="swap"
/dev/mapper/centos-root: UUID="17f09dae-88d1-4813-9738-8996819cf6ba" TYPE="xfs"
/dev/sdb2: UUID="rWS5kI-EvQy-C1yO-S1C2-QJeP-i52B-XhNRZ0" TYPE="LVM2_member"
/dev/sdb3: UUID="iwFLr7-fDHl-wdnT-mwKU-FSKq-xdEH-GcrnGD" TYPE="LVM2_member"
</pre>

Now let's install the ext4 filesystem on the first LV:


<pre>
$  mkfs.ext4 /dev/vgdata1vg/lvmyfirstlv
mke2fs 1.42.9 (28-Dec-2013)
Filesystem label=
OS type: Linux
Block size=1024 (log=0)
Fragment size=1024 (log=0)
Stride=0 blocks, Stripe width=0 blocks
76912 inodes, 307200 blocks
15360 blocks (5.00%) reserved for the super user
First data block=1
Maximum filesystem blocks=33947648
38 block groups
8192 blocks per group, 8192 fragments per group
2024 inodes per group
Superblock backups stored on blocks:
        8193, 24577, 40961, 57345, 73729, 204801, 221185

Allocating group tables: done
Writing inode tables: done
Creating journal (8192 blocks): done
Writing superblocks and filesystem accounting information: done

</pre>

Now lets install the xfs filesystem on the second LV:


<pre>
$ mkfs.xfs /dev/mapper/vgdata1vg-lvmysecondlv
meta-data=/dev/mapper/vgdata1vg-lvmysecondlv isize=256    agcount=4, agsize=25600 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=0        finobt=0
data     =                       bsize=4096   blocks=102400, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=0
log      =internal log           bsize=4096   blocks=853, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0

</pre>


Now let's recheck the status:


<pre>
$ lsblk
NAME                       MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda                          8:0    0    20G  0 disk
├─sda1                       8:1    0   500M  0 part /boot
└─sda2                       8:2    0  19.5G  0 part
  ├─centos-swap            253:0    0     2G  0 lvm  [SWAP]
  └─centos-root            253:1    0  17.5G  0 lvm  /
sdb                          8:16   0   2.1G  0 disk
├─sdb1                       8:17   0   1.1G  0 part
├─sdb2                       8:18   0   500M  0 part
│ └─vgdata1vg-lvmyfirstlv  253:2    0   300M  0 lvm
└─sdb3                       8:19   0 503.6M  0 part
  └─vgdata1vg-lvmysecondlv 253:3    0   400M  0 lvm
sr0                         11:0    1  55.4M  0 rom
$ blkid
/dev/sr0: UUID="2015-03-02-14-24-20-00" LABEL="VBOXADDITIONS_4.3.24_98716" TYPE="iso9660"
/dev/sda1: UUID="547c78ce-34b5-4e31-84d2-bf72b196fa57" TYPE="xfs"
/dev/sda2: UUID="JnfbeO-XPAG-AS0O-Mrtw-hnwn-7hqf-Mhk46t" TYPE="LVM2_member"
/dev/mapper/centos-swap: UUID="94263d84-5357-4566-9987-fc74ca946a96" TYPE="swap"
/dev/mapper/centos-root: UUID="17f09dae-88d1-4813-9738-8996819cf6ba" TYPE="xfs"
/dev/sdb2: UUID="rWS5kI-EvQy-C1yO-S1C2-QJeP-i52B-XhNRZ0" TYPE="LVM2_member"
/dev/sdb3: UUID="iwFLr7-fDHl-wdnT-mwKU-FSKq-xdEH-GcrnGD" TYPE="LVM2_member"
<strong>/dev/mapper/vgdata1vg-lvmyfirstlv: UUID="c35e024a-fd04-45de-afc8-566c634593cd" TYPE="ext4"
/dev/mapper/vgdata1vg-lvmysecondlv: UUID="02e140d7-0323-40d2-b7ee-5728f06305e3" TYPE="xfs"
</strong>
</pre>

As you can see, our new block devices (which are the file representation of our logical volumes) are now listed because they have filesystems installed on them. 



<h2>Mount logical volumes</h2>
Now that our logical volumes are ready to be mounted. First let's create out mountpoints:

<pre>
$ mkdir /tmp/lvdata1 /tmp/lvdata2
</pre>

Now we can either mount them manually, or automount them by adding the necessary entry in the /etc/fstab file. 
In this demo we'll manually mount the first LV and automount the second LV. 

Let's first do the manual mount:



<pre>
$ lsblk
NAME                       MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda                          8:0    0    20G  0 disk
├─sda1                       8:1    0   500M  0 part /boot
└─sda2                       8:2    0  19.5G  0 part
  ├─centos-swap            253:0    0     2G  0 lvm  [SWAP]
  └─centos-root            253:1    0  17.5G  0 lvm  /
sdb                          8:16   0   2.1G  0 disk
├─sdb1                       8:17   0   1.1G  0 part
├─sdb2                       8:18   0   500M  0 part
│ └─vgdata1vg-lvmyfirstlv  253:2    0   300M  0 lvm
└─sdb3                       8:19   0 503.6M  0 part
  └─vgdata1vg-lvmysecondlv 253:3    0   400M  0 lvm
sr0                         11:0    1  55.4M  0 rom

$ mount /dev/vgdata1vg/lvmyfirstlv  /tmp/lvdata1

$ lsblk
NAME                       MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda                          8:0    0    20G  0 disk
├─sda1                       8:1    0   500M  0 part /boot
└─sda2                       8:2    0  19.5G  0 part
  ├─centos-swap            253:0    0     2G  0 lvm  [SWAP]
  └─centos-root            253:1    0  17.5G  0 lvm  /
sdb                          8:16   0   2.1G  0 disk
├─sdb1                       8:17   0   1.1G  0 part
├─sdb2                       8:18   0   500M  0 part
<strong>│ └─vgdata1vg-lvmyfirstlv  253:2    0   300M  0 lvm  /tmp/lvdata1</strong>      # success
└─sdb3                       8:19   0 503.6M  0 part
  └─vgdata1vg-lvmysecondlv 253:3    0   400M  0 lvm
sr0                         11:0    1  55.4M  0 rom


</pre>

Another check we can do is:


<pre>
df -h
Filesystem                         Size  Used Avail Use% Mounted on
/dev/mapper/centos-root             18G  4.3G   14G  25% /
devtmpfs                           488M     0  488M   0% /dev
tmpfs                              497M   80K  497M   1% /dev/shm
tmpfs                              497M  7.0M  490M   2% /run
tmpfs                              497M     0  497M   0% /sys/fs/cgroup
/dev/sda1                          497M  143M  354M  29% /boot
<strong>/dev/mapper/vgdata1vg-lvmyfirstlv  283M  2.1M  262M   1% /tmp/lvdata1</strong>
</pre>

Now lets automate the mounting of the second LV by adding the entry in the /etc/fstab file:


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
<strong>/dev/mapper/vgdata1vg-lvmysecondlv   /tmp/lvdata2             xfs     defaults 0 0</strong>

</pre>

Now let's test our new entry by trying the automount:


<pre>
$ lsblk
NAME                       MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda                          8:0    0    20G  0 disk
├─sda1                       8:1    0   500M  0 part /boot
└─sda2                       8:2    0  19.5G  0 part
  ├─centos-swap            253:0    0     2G  0 lvm  [SWAP]
  └─centos-root            253:1    0  17.5G  0 lvm  /
sdb                          8:16   0   2.1G  0 disk
├─sdb1                       8:17   0   1.1G  0 part
├─sdb2                       8:18   0   500M  0 part
│ └─vgdata1vg-lvmyfirstlv  253:2    0   300M  0 lvm  /tmp/lvdata1
└─sdb3                       8:19   0 503.6M  0 part
  └─vgdata1vg-lvmysecondlv 253:3    0   400M  0 lvm
sr0                         11:0    1  55.4M  0 rom

$ mount -a

$ lsblk
NAME                       MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda                          8:0    0    20G  0 disk
├─sda1                       8:1    0   500M  0 part /boot
└─sda2                       8:2    0  19.5G  0 part
  ├─centos-swap            253:0    0     2G  0 lvm  [SWAP]
  └─centos-root            253:1    0  17.5G  0 lvm  /
sdb                          8:16   0   2.1G  0 disk
├─sdb1                       8:17   0   1.1G  0 part
├─sdb2                       8:18   0   500M  0 part
│ └─vgdata1vg-lvmyfirstlv  253:2    0   300M  0 lvm  /tmp/lvdata1
└─sdb3                       8:19   0 503.6M  0 part
  └─vgdata1vg-lvmysecondlv 253:3    0   400M  0 lvm  /tmp/lvdata2
sr0                         11:0    1  55.4M  0 rom
</pre>

We can confirm that this has worked using the mount, df, and the blkid commands:

<pre>
$ df -h
Filesystem                          Size  Used Avail Use% Mounted on
/dev/mapper/centos-root              18G  4.3G   14G  25% /
devtmpfs                            488M     0  488M   0% /dev
tmpfs                               497M   80K  497M   1% /dev/shm
tmpfs                               497M  7.0M  490M   2% /run
tmpfs                               497M     0  497M   0% /sys/fs/cgroup
/dev/sda1                           497M  143M  354M  29% /boot
/dev/mapper/vgdata1vg-lvmyfirstlv   283M  2.1M  262M   1% /tmp/lvdata1
<strong>/dev/mapper/vgdata1vg-lvmysecondlv  397M   21M  377M   6% /tmp/lvdata2
</strong>


$ blkid | grep "vgdata1vg"
/dev/mapper/vgdata1vg-lvmyfirstlv: UUID="c35e024a-fd04-45de-afc8-566c634593cd" TYPE="ext4"
/dev/mapper/vgdata1vg-lvmysecondlv: UUID="02e140d7-0323-40d2-b7ee-5728f06305e3" TYPE="xfs"



$ mount | grep "^/dev"
/dev/mapper/centos-root on / type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
/dev/sda1 on /boot type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
<strong>/dev/mapper/vgdata1vg-lvmyfirstlv on /tmp/lvdata1 type ext4 (rw,relatime,seclabel,data=ordered)
/dev/mapper/vgdata1vg-lvmysecondlv on /tmp/lvdata2 type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
</strong>

</pre>


Now if we did go ahead and rebooted our machine then only our 2nd LV would get automounted since there is an entry for it in the /etc/fstab file.