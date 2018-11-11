---
ID: 392
post_title: Creating Partitions on CentOS/RHEL 7
author: sher
post_excerpt: "A block devices is usually a physical device that's used for storing data, e.g. Hard drives and solid state drives. A partition is basically a way to organise a block device's storage into smaller segments, that means creating partitions allows you to use a percentage of your block device's storage space for a specific purpose and leave the rest available for other uses."
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-creating-partitions
published: true
post_date: 2018-03-30 00:00:00
---
A block devices is usually a physical device that's used for storing data, e.g. Hard drives and solid state drives. A partition is basically a way to organise a block device's storage into smaller segments, that means creating partitions allows you to use a percentage of your block device's storage space for a specific purpose and leave the rest available for other uses. If you want to use all the storage of a block device for a particular purpose, then you don't need to create partitions.

A partition can be used in lots of different ways. One of the common ways is to use it as an ordinary filesystem. There are 3 steps you can do this:

<ol>
	<li><strong>Create the partition</strong> - this is done using the “fdisk” tool. We'll cover how to do this in this article.</li>
	<li><strong>Format the partition</strong> - This is also known as installing a filesystem. It is done using the “mkfs” command</li>
	<li><strong>Mount the partition</strong> - done using the “mount” tool (and automate it by updating the fstab config file)</li>
</ol>

We have created a vagrant project on github to follow along this example.

We can see what block devices we have attached to our system using the <code>lsblk</code> command:

<pre>
$ lsblk
NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
<strong>sda</strong>               8:0    0  64G  0 <strong>disk</strong>
├─sda1            8:1    0   1G  0 part /boot
└─sda2            8:2    0  63G  0 part
  ├─centos-root 253:0    0  41G  0 lvm  /
  ├─centos-swap 253:1    0   2G  0 lvm  [SWAP]
  └─centos-home 253:2    0  20G  0 lvm  /home
<strong>sdb</strong>               8:16   0   2G  0 <strong>disk</strong>
</pre>


Here we have 2 block devices (as indicated by the disk label), sda and sdb. In this example, sda is made up of 2 partitions, sda1 and sda2.  At the moment sdb doesn't have any partitions, however it might contain data. You can check if that's the case using the <code>blkid</code> command:

<pre>
$ blkid
/dev/sda1: UUID="9e33c3c1-98df-4fe1-a66f-13ffbaa5f154" TYPE="xfs"
/dev/sda2: UUID="jE1A3D-X4dw-pi2u-BYxC-fvoQ-vVtn-R25igD" TYPE="LVM2_member"
/dev/mapper/centos-root: UUID="9e702351-4e25-449a-a586-9d713265a6e2" TYPE="xfs"
/dev/mapper/centos-swap: UUID="ae39ba11-dadd-498b-a279-04aaee5abe2c" TYPE="swap"
/dev/mapper/centos-home: UUID="1d84b924-8491-4e8a-9476-b969bfc5de2e" TYPE="xfs"
</pre>

Since sdb isn't listed here, it means that sdb doesn't have a filesystem installed on it and therefore it's an empty block device. The block devices are represented as files in the <code>/dev</code> directory. Block devices are indicated by the letter 'b':

<pre>
$ ll /dev/ | grep '^b'
<strong>b</strong>rw-rw----. 1 root    disk    253,   0 Mar 29 16:23 dm-0
<strong>b</strong>rw-rw----. 1 root    disk    253,   1 Mar 29 16:23 dm-1
<strong>b</strong>rw-rw----. 1 root    disk    253,   2 Mar 29 16:23 dm-2
<strong>b</strong>rw-rw----. 1 root    disk      8,   0 Mar 29 16:23 sda
<strong>b</strong>rw-rw----. 1 root    disk      8,   1 Mar 29 16:23 sda1
<strong>b</strong>rw-rw----. 1 root    disk      8,   2 Mar 29 16:23 sda2
<strong>b</strong>rw-rw----. 1 root    disk      8,  16 Mar 29 16:23 sdb
</pre>

Note the dm-x devices are note partitions or block devices. They are actually file representations of logical volumes (see the content of '/dev/mapper/' directory). A partition's name is the same as it's parent block device followed by a number, e.g. sda1 and sda2.  

So far we have seen that there are no partitions (e.g. sdb1, sdb2, sdb3...etc) created from sdb, and sdb isn't being used for anything else. We can start using sdb as a whole block device or we can use a part of sdb for a specific purpose, by creating partitions. In our case we will created several partitions from sdb1 and sdb2.   

Partitions are created using the <code>fdisk</code> command. First off, the fdisk command can be used to give a detailed overview of our block devices, partitions, and logical volumes:

<pre>
$ fdisk -l

Disk /dev/sda: 68.7 GB, 68719476736 bytes, 134217728 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x000bfd84

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048     2099199     1048576   83  Linux
/dev/sda2         2099200   134217727    66059264   8e  Linux LVM

Disk /dev/sdb: 2147 MB, 2147483648 bytes, 4194304 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/mapper/centos-root: 44.0 GB, 44006637568 bytes, 85950464 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/mapper/centos-swap: 2147 MB, 2147483648 bytes, 4194304 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/mapper/centos-home: 21.5 GB, 21483225088 bytes, 41959424 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
</pre>

Also if you just want info about a particular resource, e.g. sda2, then do:


<pre>
$ fdisk -l /dev/sda1

Disk /dev/sda1: 1073 MB, 1073741824 bytes, 2097152 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
</pre>


fdisk main function is that it is an interactive tool to create/manage/delete partitions. So to start creating partitions from sdb, we do:


<pre>
$ fdisk /dev/sdb
Welcome to fdisk (util-linux 2.23.2).

Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table
Building a new DOS disklabel with disk identifier 0x2f383010.

Command (m for help):

</pre>

Note, the warning message, this is expected since it is a new hdd that hasnt been partitioned yet.


If we select the "m" option, we get:


<pre>
Command (m for help): m
Command action
   a   toggle a bootable flag
   b   edit bsd disklabel
   c   toggle the dos compatibility flag
   d   delete a partition
   g   create a new empty GPT partition table
   G   create an IRIX (SGI) partition table
   <strong>l   list known partition types                      # useful</strong>
   m   print this menu
  <strong> n   add a new partition                             # useful</strong>
   o   create a new empty DOS partition table
   <strong>p   print the partition table                       # useful</strong>
   q   quit without saving changes
   s   create a new empty Sun disklabel
   <strong>t   change a partition's system id                  # needed when creating LVMs</strong>
   u   change display/entry units
   v   verify the partition table
   <strong>w   write table to disk and exit                    # usefu</strong>l
   x   extra functionality (experts only)

Command (m for help):

</pre>

Now, let's try to (p)rint the partition table for our new device:


<pre>
Command (m for help): p

Disk /dev/sdb: 2206 MB, 2206806016 bytes, 4310168 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x2f383010

   Device Boot      Start         End      Blocks   Id  System

Command (m for help):

</pre>


At the moment this table is currently empty. That's because the data that the "p" option displays is actually stored in a special internal partition where the MBR resides. This internal partition is very small, i.e. less than 1kb in size, and it holds information about the partition setup for the rest of the hdd. But since this is a blank new device, it's doesn't even have this special internal partition either.


Now let's use option "n" start creating a new partition:


<pre>
Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p):
</pre>

At this point you'll discover that there are different types of partitions.
 

<h2>Primary, Extended, and Logical partition types</h2>

You can only create up to a maximum of 4 partitions on a given hdd. This limit is caused by the fact that the MBR's partition is only 1-2 kb in size and hence can't hold information for more than 4 partitions. As a way to overcome this restriction, the concept of Primary/Extended/Logical partitions was introduced.  

<ul>
	<li><strong>Primary partition</strong> - These are the convention partitions that would create by default. If you only plan to create 4 partitions or less, then you only need to create primary partition and you don't have to worry about extended and logical partitions.</li>
	<li><strong>Extended partition</strong> - If you want to create more than 4 partitions on a hdd, now or in the future, then you need to create one of the partition as an extended partition. An extended partition is not actually a partition, instead it is essentially an empty container inside which you can create (logical) partitions. You cannot store data inside an extended partition directly, instead you first have to create logical partition inside the extended partition. You can only create one extended partition per hdd</li>
	<li><strong>logical partition</strong> - These are the names of any partitions created inside an extended partition. OS uses logical partitions in the same was as a typical primary partition</li>
</ul>


Best practice tip: You should always create an extended partition in the off chance that you might need to have more than 4 partitions. 

In my case, I'll create my first primary partition  which is going to be 10MB in size.:


<pre>
Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p):
Using default response p
Partition number (1-4, default 1):
First sector (2048-4310167, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-4310167, default 4310167): +10M
Partition 1 of type Linux and of size 10 MiB is set

</pre> 

Notice that that the first sector start at 2048 rather than 0. That's because the 0-2047 is reserved for setting up the MBR's special internal partition. 


Now if we view the partition table again we have:


<pre>
Command (m for help): p

Disk /dev/sdb: 2206 MB, 2206806016 bytes, 4310168 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x2f383010

   Device Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048       22527       10240   83  Linux
</pre>

Note, this hasn't been set yet, we have to use the "w" option to apply the changes. At this stage it is just showing what would happen if we select the "w" option. 

Let's now repeat the process to create 3 10MB primary partitions in total, after which our partition table now looks like:


<pre>
Command (m for help): p

Disk /dev/sdb: 2206 MB, 2206806016 bytes, 4310168 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x2f383010

   Device Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048       22527       10240   83  Linux
/dev/sdb2           22528       43007       10240   83  Linux
/dev/sdb3           43008       63487       10240   83  Linux

Command (m for help):

</pre> 

Let's go ahead and create an extended partition now:


<pre>
Command (m for help): n
Partition type:
   p   primary (3 primary, 0 extended, 1 free)
   e   extended
Select (default e):                             # notice that the default is now "e", which is nice. 
Using default response e
Selected partition 4
First sector (63488-4310167, default 63488):
Using default value 63488
Last sector, +sectors or +size{K,M,G} (63488-4310167, default 4310167): +1024M        # tip: leave this blank!
Partition 4 of type Extended and of size 1 GiB is set

Command (m for help):
</pre> 

Since our extended partition is a container for other partitions, I have set this too 1GB in size. 

Tip: best to leave the last sector blank, so that you fully use up your hdd's remaining available space. 

Now if we try to create yet another partition, then fdisk is smart enough to automatically default to creating a logical partition now:




<pre>
Command (m for help): n
All primary partitions are in use
Adding logical partition 5
First sector (65536-2160639, default 65536):
Using default value 65536
Last sector, +sectors or +size{K,M,G} (65536-2160639, default 2160639): +10M
Partition 5 of type Linux and of size 10 MiB is set

Command (m for help):

</pre>


let's do this a couple more times so that we have 3 logical partitions in total, then let's see the partition table again:


<pre>
Command (m for help): p

Disk /dev/sdb: 2206 MB, 2206806016 bytes, 4310168 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x2f383010

   Device Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048       22527       10240   83  Linux
/dev/sdb2           22528       43007       10240   83  Linux
/dev/sdb3           43008       63487       10240   83  Linux
/dev/sdb4           63488     2160639     1048576    5  Extended
/dev/sdb5           65536       86015       10240   83  Linux
/dev/sdb6           88064      108543       10240   83  Linux
/dev/sdb7          110592      131071       10240   83  Linux

Command (m for help):
</pre>
 
As you can see, the Id/System for all our primary/logical partitions has defaulted to 83/Linux, . Use option "l" to view the full list of partition types:


<pre>
Command (m for help): l

 0  Empty           24  NEC DOS         81  Minix / old Lin bf  Solaris
 1  FAT12           27  Hidden NTFS Win <mark>82  Linux swap / So</mark> c1  DRDOS/sec (FAT-
 2  XENIX root      39  Plan 9          <mark>83  Linux</mark>           c4  DRDOS/sec (FAT-
 3  XENIX usr       3c  PartitionMagic  84  OS/2 hidden C:  c6  DRDOS/sec (FAT-
 4  FAT16 <32M      40  Venix 80286     85  Linux extended  c7  Syrinx
 <mark>5  Extended</mark>        41  PPC PReP Boot   86  NTFS volume set da  Non-FS data
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

Command (m for help):


</pre>

 The ones highlighted above are most commonly used. Here is a summary of each of these:


<ul>
	<li><strong>5/extended</strong> - this is an extended partition.</li>
	<li><strong>83/Linux</strong> - this is the default and indicates that this partition will be used for installing a standard filesystem on it</li>
	<li><strong>8e/LVM</strong> - used for creating physical volumes</li>
	<li><strong>Swap</strong> - Used for creating pseudo ram, covered later</li>
</ul>

Let's now go ahead, and apply our changes by saving this new partition table (using option "w") to the special internal mbr's partition. 

<pre>
Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
[root@localhost dev]#
</pre>

When using option "w" you might get the following error message:


<pre>
Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.

WARNING: Re-reading the partition table failed with error 16: Device or resource busy.
The kernel still uses the old table. The new table will be used at
the next reboot or after you run partprobe(8) or kpartx(8)
Syncing disks.
</pre>

<strong>IMPORTANT: When this happens, you may need to then reboot the machine for the changes to actually take affect. </strong>

Instead of rebooting the machine, you may be able to get away with just running the <code>partprobe</code> or <code>partx</code> commands, although doesnt always work. 



<pre>
$ whatis partx
partx (8)            - tell the Linux kernel about the presence and numbering of on-disk partitions
$ whatis partprobe
partprobe (8)        - inform the OS of partition table changes
</pre>





Note: you don't need to restart your machine if fdisk has successfully applied the changes. You can check if the changes have already been applied by checking if the block device files exist:


<pre>
$ ls -l /dev | grep "sdb"
brw-rw----. 1 root disk      8,  16 May 17 19:22 sdb
brw-rw----. 1 root disk      8,  17 May 17 19:22 sdb1
brw-rw----. 1 root disk      8,  18 May 17 19:22 sdb2
brw-rw----. 1 root disk      8,  19 May 17 19:22 sdb3
brw-rw----. 1 root disk      8,  20 May 17 19:22 sdb4
brw-rw----. 1 root disk      8,  21 May 17 19:22 sdb5
brw-rw----. 1 root disk      8,  22 May 17 19:22 sdb6
brw-rw----. 1 root disk      8,  23 May 17 19:22 sdb7
</pre>

Another way to check is by using lsblk:

<pre>
$ lsblk
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   20G  0 disk
├─sda1            8:1    0  500M  0 part /boot
└─sda2            8:2    0 19.5G  0 part
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

The lsblk shows a lot more info than just a list hdds/partitions, e.g. it shows which partition is being used as part of lvm, which partitions/HDD are currently mounted,...and etc.  


Note: if you want to create a GPT based partition, then you need to use the "gdisk" command. The gdisk command works in a very similar way to the fdisk command. 


On the job tip: It's difficult to automate fdisk by writing shell scripts, the option being the 'EOF' approach to simulate inputs into the interactive prompts. However the gdisk rpm comes with <a href="http://www.rodsbooks.com/gdisk/sgdisk-walkthrough.html">sgdisk</a> which is a tool that's specifically designed for creating partitions using shell scripts. 


<h2>Alternatives to fdisk</h2>
fdisk is actually quite an old tool and in fact it's already showing it's age, namely :

<ul>
	<li>It can't be used on block devices that are bigger than 2TB</li>
	<li>You can only create a maximum of 4 partitions. The fact that you can create extended+logical partitions is more of a workaround to the 4 partition limit</li>
	<li>You can only create partitions by using fdisk's interactive mode. Which means it's not that straight forward to create partitions via shell scripts. The only option being using the <a href="https://www.tldp.org/LDP/abs/html/here-docs.html">bash heredoc</a> technique</li>
</ul>

That's why its better newer partition tools, namely <a href="https://www.rodsbooks.com/gdisk/walkthrough.html">gdisk</a> and <a href="https://www.gnu.org/software/parted/manual/parted.html">parted</a>. For example gdisk let's you create a maximum of 128 partitions. gdisk supports block devices that are >2TB, and it has a dedicated utility for automating partitioning via shell scripts. 

[post-content post_name=rhsca-quiz] 

[accordion]
[toggle title="What command do you use to create MBR based partitions for /dev/sdb?"]
$ fdisk /dev/sdb
[/toggle]
[toggle title="What is the command to create GPT based partitions for the device /dev/sdb?"]
$ gdisk /dev/sdb
[/toggle]
[toggle title="What is the command to refresh your partition tables?"]
$ partprobe   # or you can reboot your machine
[/toggle]
[toggle title="How many types of partitions are there and what are they called?"]
3 types: 
- primary partitions
- extended partitions
- logical partitions.
[/toggle]
[toggle title="What is the command to list all storage devices in /dev?"]
$ ls -l /dev | grep "^b"
[/toggle]
[toggle title="The 3 steps to creating a partition and bring it into use?"]
1 - Create the partition (using fdisk or gdisk)
2 - Format the partition (using mkfs)
3 - Mount the partition (using the “mount” tool)
[/toggle]
[toggle title="What is the command to display all your storage devices along with corresponding partitions in a visual tree structure?"]
$ lsblk
[/toggle]
[/accordion]