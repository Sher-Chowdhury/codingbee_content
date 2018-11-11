---
ID: 454
post_title: Creating swap disks
author: sher
post_excerpt: >
  Swap disks are a special type of storage
  that are used to act like extra system
  ram. This is handy if your machine is
  running low on ram, although using swap
  can degrade performance.
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-creating-swap-disks
published: true
post_date: 2015-09-27 00:00:00
---
Swap disks are a special type of storage that are used to act like extra system ram. This is handy if your machine is running low on ram, although using swap can degrade performance. To see your ram info, you use the <em>free</em> command:


<pre>
$ free -m
              total        used        free      shared  buff/cache   available
Mem:            993         517         306          13         169         314
Swap:          1023         217         806
</pre>

Here you can see how much swap memory is currently active. 


You can make a swap disk out of either a disk partition, or from an LV. The approach varies slight with each approach. 

<h2>Create a Swap disk from a disk partition</h2>

First you need to create a partition, that is of the "Linux swap" partition type:

<pre>
Command (m for help): l

 0  Empty           24  NEC DOS         81  Minix / old Lin bf  Solaris
 1  FAT12           27  Hidden NTFS Win <mark>82  Linux swap / So</mark> c1  DRDOS/sec (FAT-
 2  XENIX root      39  Plan 9          83  Linux           c4  DRDOS/sec (FAT-
 3  XENIX usr       3c  PartitionMagic  84  OS/2 hidden C:  c6  DRDOS/sec (FAT-
 4  FAT16 <32M      40  Venix 80286     85  Linux extended  c7  Syrinx
 5  Extended        41  PPC PReP Boot   86  NTFS volume set da  Non-FS data
 6  FAT16           42  SFS             87  NTFS volume set db  CP/M / CTOS / .
 7  HPFS/NTFS/exFAT 4d  QNX4.x          88  Linux plaintext de  Dell Utility
 8  AIX             4e  QNX4.x 2nd part 8e  Linux LVM       df  BootIt
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

</pre> 


Next we need to install the "swap filesystem":

<pre>
$ mkswap -L {swap-name} /dev/sdb1
mkswap: /dev/sdb1: warning: wiping old xfs signature.
Setting up swapspace version 1, size = 102396 KiB
no label, UUID=2e80abd8-ee86-4d06-b1b5-09ad66d17cc0
$
</pre>
Note: assigning a name with "-L" is optional, but is definitely recommended. Next we can activate the swap using the swapon command:

<pre>
$ swapon /dev/sdb1
</pre>

You can then check this has worked using the <code>free -m</code> command.

To list all your active swap disks, you can do:

<pre>
$ swapon -s
Filename                                Type            Size    Used    Priority
/dev/dm-1                               partition       1048572 224464  -1
/dev/sdb1                               partition       102396  0       -2
</pre> 

Note: this doesn't list inactive swaps. It only list active swaps only. 
 

You can also deactivate the swap with the swapoff command:

<pre>
$ swapoff /dev/sdb1
</pre>

Another way to deactivate the swap space is by rebooting the machine. That's because activating a swap using "swapon" isn't persistant. To make it persistant, you have to add it to the <code>/etc/fstab</code> file. More about this later.  


<h2>Create a Swap disk from a Logical Volume</h2>

First you create an LV, unless you already have one available.
 
<pre>
$ mkswap /dev/vg-name/lv-name
</pre>

Then you can use enable/disable swaps as shown earlier. 


<h2>Automouniting swap disks</h2>
If you want the new swap to become active at boot time, then you need add the new swap to the fstab file, here are the entries that you need to add:


col 1: “LABEL={swap’s nickname}”  or "UUID=..."

col 2: swap              # since mount-points are not applicable for swaps.

col 3: swap       # Since filesystems like ext4 are not applicable.               

col 4: defaults

col 5: 0                    # since you don’t need to keep backups of temp data.

col 6: 0                 # since you don’t need to do fsck because it will only contain temp data


You can test whether you have entered everything in fstab correctly by running the following command:


<pre>
$ swapon -a
</pre>

Here swapon will read the fstab file and start all swaps it can find. 

You can then use <code>free -m</code> and <code>swapon -s</code> to check everything is as expected. 

Likewise, to turn off all swaps listed in the fstab file, you do:

<pre>
$ swapoff -a
</pre>
 

 






Creating a swap partition is quite similar to how you create a filesystem partition, here's a side-by-side comparison of the 2 processes:

&nbsp;

&nbsp;
[table id=1 /]
&nbsp;









[post-content post_name=rhsca-quiz] 

[accordion]
[toggle title="What is the command to check your ram status?"]
$ free -m

[/toggle]
[toggle title="What is the command to create a new partition from /dev/sdb, which is used for creating a swap?"]
$ fdisk /dev/sdb   # ensure you set the type to set the type as "Linux swap"
[/toggle]
[toggle title="What is the command to install the 'swap filesystem' on /dev/sb3, and name it extramemory?"]
$ mkswap -L extramemory /dev/sdb3
[/toggle]
[toggle title="What is the command to activate the swap, /dev/sdb3?"]
$ swapon /dev/sdb3
[/toggle]
[toggle title="What is the command to check if the above has worked?"]
$ free -m
[/toggle]
[toggle title="What is the command to list all active swaps?"]
$ swapon -s
[/toggle]
[toggle title="What is the command to deactivate the swap, /dev/sdb3?"]
$ swapoff /dev/sdb3
[/toggle]
[toggle title="What is the entry to add into /etc/fstab to auto activate the swap, with the label, 'extramemory'?"]
LABEL=extramemory  swap  swap  defaults  0  0
[/toggle]
[toggle title="What is the command to test that the above entry is correct?"]
$ swapon -a
[/toggle]
[toggle title="What is the command to deactivate all swaps?"]
$ swapoff -a
[/toggle]
[/accordion]