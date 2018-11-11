---
ID: 400
post_title: 'RHCSA &#8211; Extending and shrinking a Logical Volume'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-extending-and-shrinking-a-logical-volume
published: true
post_date: 2015-05-23 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

In the following set of questions let's assume we have a logical volume called 'lvmyfirstlv' that has been created from the volume group called 'vgdata1vg'.

[accordion]
[toggle title="What is the command to check what volume group the logical volume 'lvmyfirstlv', has been created from?"]
$ lvs
[/toggle]
[toggle title="What is the command to check how much available disk space each of your volume group have?"]
$ vgs
[/toggle]
[toggle title="What is the command to make lvmyfirstlv 100MB bigger using available space from the volugme group, 'vgdata1vg'?"]
$ lvextend /dev/vgdata1vg/lvmyfirstlv -L +100M
[/toggle]
[toggle title="What is the command to add all the available space on vgdata1vg to lvmyfirstlv?"]
$ lvextend -l +100%FREE /dev/vgdata1vg/lvmyfirstlv
[/toggle]
[toggle title="What 2 commands can you run to check that the lvextend command has been successful?"]
$ lvs
# and
$ vgs
[/toggle]
[toggle title="What 2 command can you run to check what filesystem is installed on the logical volume, lvmyfirstlv?"]
$ mount | grep lvmyfirstlv
# or
$ blkid | grep lvmyfirstlv
[/toggle]
[toggle title="What is the command to make the ext4 based logical volume, lvmyfirstlv, aware of the extra space (that has been allocated from the volume group, vgdata1vg) and make use of it?"]
$ resize2fs /dev/vgdata1vg/lvmyfirstlv
[/toggle]
[toggle title="If however lvmyfirstlv has an xfs filesystem. then what command do you need to run instead of the above one?"]
$ xfs_growfs {lvmyfirstlv-mountpoint}
[/toggle]
[toggle title="How can you avoid using resize2fs, grow_fs,....etc?"]
Use the "-r" option when using the lvextend command:
$ lvextend -l +100%FREE -r /dev/vgdata1vg/lvmyfirstlv
[/toggle]
[toggle title="What command can you run to check if the filesystem resizing has been successful?"]
$ df -h
[/toggle]
[toggle title="What type of filesystem isn't possible to shrink?"]
The xfs filesystem. The workaround is to create a smaller lv with xfs on it, copy across the data, from the soon-to-be-decommisioned filesystem. Then delete the old file system. 
[/toggle]
[toggle title="What is the command that lets you check by how much you can shrink a logical volume, based on unused space??"]
$ df -h
[/toggle]
[toggle title="What is the command to shrink the filesystem that's on the logical volume, lvmyfirstlv, to 350MB (assuming this logical volume was created from the volume group called 'vgdata1vg')?"]
$ resize2fs /dev/vgdata1vg/lvmyfirstlv 350M
[/toggle]
[toggle title="What do you need to take note of after running the above command?"]
The number of blocks that new filesystem is made up of. 
[/toggle]
[toggle title="Assuming that the new filesystem is 358400 blocks in size, then what command do you run to reduce the lvmyfirstlv logical volume to this size?"]
$ lvreduce -L 358400K /dev/vgdata1vg/lvmyfirstlv
[/toggle]
[toggle title="What command can you run to avoid using the resize2fs command, and also shrink lvmyfirstlv in a single command?"]
$ lvreduce -rL 350M /dev/vgdata1vg/lvmyfirstlv
[/toggle]
[/accordion]

<hr/>






From time to time you will often want to resize your logical volume. For example you may want to increase an logical volume's capacity because it's running out of disk space. Or you might want to shrink a 10TB LV to 1GB because you have discovered that your logical volume will never need more than 800MB during it's lifetime. 


When re-sizing your LV, you have to always be mindful of the filesystem that exists on it already. During a filesystem install on a logical volume, that whole logical volume gets scanned and indexed. Consequently the filesystem will set up a boundary that occupies the entire logical volume's diskspace. Based on this, we have to do things differently depending on whether we want to extend or shrink a logical volume. 


<h2>Extending a logical volume</h2>

Before you can extend the logical volume, you need to first check whether there is enough free capacity in the volume group. If so then we can go ahead and extend it. 


If not, then we need to free-up/extend the volume group first, either by deleting/shrinking other logical volumes that this volume group provides, or by extending the volume group by allocating more physical volumes to it. 



Let's say we want to increase the size of the logical volume, lvmyfirstlv, by 100MB. 

Let's first see how big it is currently:

<pre>
$ df -h
Filesystem                          Size  Used Avail Use% Mounted on
/dev/mapper/centos-root              18G  4.3G   14G  25% /
devtmpfs                            488M     0  488M   0% /dev
tmpfs                               497M   80K  497M   1% /dev/shm
tmpfs                               497M   14M  484M   3% /run
tmpfs                               497M     0  497M   0% /sys/fs/cgroup
/dev/sda1                           497M  143M  354M  29% /boot
<strong>/dev/mapper/vgdata1vg-lvmyfirstlv   283M  2.1M  262M   1% /tmp/lvdata1</strong>
/dev/mapper/vgdata1vg-lvmysecondlv  397M   21M  377M   6% /tmp/lvdata2
</pre>

   
At the moment it is 283MB. Now let's see which volume group that lvmyfirstlv has been created from:


<pre>
$ lvs
  LV           VG        Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  root         centos    -wi-ao----  17.51g
  swap         centos    -wi-ao----   2.00g
  lvmyfirstlv  <strong>vgdata1vg</strong> -wi-ao---- 300.00m
  lvmysecondlv vgdata1vg -wi-ao---- 400.00m
</pre>

Note, the 283MB and 300MB discrepency is caused by LVM using some of the disk space for it's internal workings. 

Now let's see if vgdata1vg has 100MB or more spare capacity:

<pre>
$ vgs
  VG        #PV #LV #SN Attr   VSize   VFree
  centos      1   2   0 wz--n-  19.51g      0
  vgdata1vg   2   2   0 wz--n- 996.00m <strong>296.00m</strong>
</pre>

Here we can see that vgdata1vg has 296MB available which is more than enough. This means that we don't need to find any more free space for vgdata1vg, we can just use the existing free space. 

We can extend lvmyfirstlv using the lvextend command:


<pre>
$ lvextend --help
  lvextend: Add space to a logical volume

lvextend
        [-A|--autobackup y|n]
        [--alloc AllocationPolicy]
        [--commandprofile ProfileName]
        [-d|--debug]
        [-f|--force]
        [-h|--help]
        [-i|--stripes Stripes [-I|--stripesize StripeSize]]
        {-l|--extents [+]LogicalExtentsNumber[%{VG|LV|PVS|FREE|ORIGIN}] |
<strong>         -L|--size [+]LogicalVolumeSize[bBsSkKmMgGtTpPeE]}
</strong>         --poolmetadatasize [+]MetadataVolumeSize[bBsSkKmMgG]}
        [-m|--mirrors Mirrors]
        [--nosync]
        [--use-policies]
        [-n|--nofsck]
        [--noudevsync]
        [-r|--resizefs]
        [-t|--test]
        [--type VolumeType]
        [-v|--verbose]
        [--version]
<strong>        LogicalVolume[Path] [ PhysicalVolumePath... ]
</strong></pre> 

Based on the help, we do:


<pre>
$ lvextend /dev/vgdata1vg/lvmyfirstlv -L +100M
  Size of logical volume vgdata1vg/lvmyfirstlv changed from 300.00 MiB (75 extents) to 400.00 MiB (100 extents).
  Logical volume lvmyfirstlv successfully resized
</pre>


Now let's check if this has been successful:

<pre>
$ lvs
  LV           VG        Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  root         centos    -wi-ao----  17.51g
  swap         centos    -wi-ao----   2.00g
  <strong>lvmyfirstlv  vgdata1vg -wi-ao---- 400.00m</strong>
  lvmysecondlv vgdata1vg -wi-ao---- 400.00m
</pre>

As you can see, it was 300MB before, but now it is 400MB. Also the available space on vgdata1vg has gone down by 100MB:

<pre>
$ vgs
  VG        #PV #LV #SN Attr   VSize   VFree
  centos      1   2   0 wz--n-  19.51g      0
  vgdata1vg   2   2   0 wz--n- 996.00m 196.00m
</pre>

However if we now take a look at df (disk free) command:


<pre>
$ df -h
Filesystem                          Size  Used Avail Use% Mounted on
/dev/mapper/centos-root              18G  4.3G   14G  25% /
devtmpfs                            488M     0  488M   0% /dev
tmpfs                               497M   80K  497M   1% /dev/shm
tmpfs                               497M   14M  484M   3% /run
tmpfs                               497M     0  497M   0% /sys/fs/cgroup
/dev/sda1                           497M  143M  354M  29% /boot
/dev/mapper/vgdata1vg-lvmyfirstlv   <strong>283M</strong>  2.1M  262M   1% /tmp/lvdata1
/dev/mapper/vgdata1vg-lvmysecondlv  397M   21M  377M   6% /tmp/lvdata2


</pre>

You'll see that this hasn't change, it is still showing the same disk size as before. 

That's because the filesystem installed on this logical volume hasn't been made aware of the new disk space. So to make it aware of it, we need to make the filesystem reindex the newly added space, which we do by running the "resize2fs" command:


<pre>
$ whatis resize2fs
resize2fs (8)        - ext2/ext3/ext4 file system resizer
[root@localhost ~]# resize2fs -help
resize2fs 1.42.9 (28-Dec-2013)
$ [-d debug_flags] [-f] [-F] [-M] [-P] [-p] device [new_size]
</pre>


Let's use this command now:


<pre>
$ resize2fs /dev/vgdata1vg/lvmyfirstlv
resize2fs 1.42.9 (28-Dec-2013)
Filesystem at /dev/vgdata1vg/lvmyfirstlv is mounted on /tmp/lvdata1; on-line resizing required
old_desc_blocks = 3, new_desc_blocks = 4
The filesystem on /dev/vgdata1vg/lvmyfirstlv is now 409600 blocks long.

</pre>

Note: if you're dealing with an xfs file system then you would replace "resize2fs" with <strong>xfs_growfs</strong>


Now let's check that this has worked:


<pre>
$ df -h
Filesystem                          Size  Used Avail Use% Mounted on
/dev/mapper/centos-root              18G  4.3G   14G  25% /
devtmpfs                            488M     0  488M   0% /dev
tmpfs                               497M   80K  497M   1% /dev/shm
tmpfs                               497M   14M  484M   3% /run
tmpfs                               497M     0  497M   0% /sys/fs/cgroup
/dev/sda1                           497M  143M  354M  29% /boot
<strong>/dev/mapper/vgdata1vg-lvmyfirstlv   380M  2.3M  355M   1% /tmp/lvdata1</strong>
/dev/mapper/vgdata1vg-lvmysecondlv  397M   21M  377M   6% /tmp/lvdata2

</pre>

As you can see the disk size is now about 100MB bigger. 


<h3>Handy lvextend options</h3> 

There are a few lvextend options that can come in very handy. First off, if you want to assign all the available free space in a volume group to a logical volume, then you can do this with the the "-l" option, like this:


<pre>
$ lvextend  -l +100%FREE /dev/vgdata1vg/lvmyfirstlv
</pre>

Also you can do the filesystem reindex as part of the lvextend command, using the -r option:

<pre>
$ lvextend -l +100%FREE -r /dev/vgdata1vg/lvmyfirstlv 
</pre>

This is handy because you don't have to worry about memorise all the various filesystem rescan commands such as resize2fs for ext2/ext3/ext4 and grow_fs for xfs,....and etc. 



<h2>Shrinking a logical volume</h2>

If you have a 400MB LV with a file system installed on it. Then that file system has also indexed the whole 400MB disk space even though not all of that disk space is being used. Let's say that disk currently holds only 150MB of data, and it's anticipated that 180MB max of diskspace will be used on this LV. In that case we will want to free up the unused disk space by shrinking the LV's disk space down to 180MB.

To do this we need to shrink the file system to 180MB first. That's because the 150MB of data that is currently stored in the lv, is actually spread across the whole 400MB LV. Hence we need to first reduce the file system so that the 150MB of data gets restacked into 180MB, so that the other 220MB is emptied out and no longer occupied by the filesystem.     

Note: It's not possible to reduce an xfs file system. 

That's why before you try to reduce a filesystem, you should always first check the type of filesystem you are trying to reduced. In my case I want to reduce the filesystem on the lv, lvmyfirstlv, by 50MB. 

First we need to check that there is at least 50MB of free space available on this file system, if there isn't then it wont be possible to shrink this filesystem by 50MB:

<pre>
$ df -h
Filesystem                          Size  Used Avail Use% Mounted on
/dev/mapper/centos-root              18G  4.3G   14G  25% /
devtmpfs                            488M     0  488M   0% /dev
tmpfs                               497M   80K  497M   1% /dev/shm
tmpfs                               497M   14M  484M   3% /run
tmpfs                               497M     0  497M   0% /sys/fs/cgroup
/dev/sda1                           497M  143M  354M  29% /boot
/dev/mapper/vgdata1vg-lvmyfirstlv   380M  2.3M  355M   1% /tmp/lvdata1
/dev/mapper/vgdata1vg-lvmysecondlv  461M   21M  441M   5% /tmp/lvdata2

</pre>

Here we have about 441MB free, so shrinking the lv by 50MB should be fine. 


Next we need to check what type of filesystem it is in order to determine if it is a type of file system that supports shrinking:


<pre>
$ mount | grep "^/dev/mapper"
/dev/mapper/centos-root on / type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
/dev/mapper/vgdata1vg-lvmyfirstlv on /tmp/lvdata1 type ext4 (rw,relatime,seclabel,data=ordered)
/dev/mapper/vgdata1vg-lvmysecondlv on /tmp/lvdata2 type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
</pre>

Here we can see it is an ext4 filesystem so we should be able to shrink this. If this was an xfs file system, then we would have been stuck and would not have been able to shrink the filesystem. In that situation, an alternative approach would be have been to create a new (smaller) LV with xfs installed on it. copy across all the data from the original LV. Then destroy the old LV, since you can't shrink it's filesystem. 

    
Next we need to unmount the LV (that's because it's not possible to shrink the filesystem while it is use). 
Before you unmount, you need to first ensure that there are no services/processes that are using this filesystem:

<pre>
$ fuser -mv /dev/mapper/vgdata1vg-lvmyfirstlv
                     USER        PID ACCESS COMMAND
/dev/mapper/vgdata1vg-lvmyfirstlv:
                     root       3855 ...e. puppet
                     root       3996 f.... rsyslogd
</pre>

If any of the process are run via a service then use the systemctl command to stop them. Otherwise use the kill command (but use -9 option as a last resort). Then you do the umount:

<pre>
$ umount /dev/mapper/vgdata1vg-lvmyfirstlv
</pre>


Let's confirm that it has been unmounted:


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
<strong>│ └─vgdata1vg-lvmyfirstlv  253:2    0   400M  0 lvm</strong>
└─sdb3                       8:19   0 503.6M  0 part
  └─vgdata1vg-lvmysecondlv 253:3    0   464M  0 lvm  /tmp/lvdata2
sr0                         11:0    1  55.4M  0 rom
</pre>


Now if reduce the LV's size by 50MB, then the new size will be 350MB. We now use the resize2fs command, lets first look at it's usage info:


<pre>
$ resize2fs -help
resize2fs 1.42.9 (28-Dec-2013)
Usage: resize2fs [-d debug_flags] [-f] [-F] [-M] [-P] [-p] device [new_size]
</pre>

Based on the help info, we do:

<pre>
$ resize2fs /dev/vgdata1vg/lvmyfirstlv 350M
resize2fs 1.42.9 (28-Dec-2013)
Please run 'e2fsck -f /dev/vgdata1vg/lvmyfirstlv' first.
</pre>
 
Here we are prompted to check the filesystem's integrity first before we do the resize, so let's do as prompted:


<pre>
$ e2fsck -f /dev/vgdata1vg/lvmyfirstlv
e2fsck 1.42.9 (28-Dec-2013)
Pass 1: Checking inodes, blocks, and sizes
Pass 2: Checking directory structure
Pass 3: Checking directory connectivity
Pass 4: Checking reference counts
Pass 5: Checking group summary information
/dev/vgdata1vg/lvmyfirstlv: 11/101200 files (0.0% non-contiguous), 23297/409600 blocks
</pre>

Now let's try the resize2fs command again:


<pre>
$ resize2fs /dev/vgdata1vg/lvmyfirstlv 350M
resize2fs 1.42.9 (28-Dec-2013)
Resizing the filesystem on /dev/vgdata1vg/lvmyfirstlv to 358400 (1k) blocks.
The filesystem on /dev/vgdata1vg/lvmyfirstlv is now 358400 blocks long.

</pre>
  
Note here that it states the new size in terms of kilobyte blocks. We use this when we shrink the logical volume. 

To shrink the lv, we use the lvreduce command:


<pre>
$ lvreduce --help
  lvreduce: Reduce the size of a logical volume

lvreduce
        [-A|--autobackup y|n]
        [--commandprofile ProfileName]
        [-d|--debug]
        [-f|--force]
        [-h|--help]
        {-l|--extents [-]LogicalExtentsNumber[%{VG|LV|FREE|ORIGIN}] |
         -L|--size [-]LogicalVolumeSize[bBsSkKmMgGtTpPeE]}
        [-n|--nofsck]
        [--noudevsync]
        [-r|--resizefs]
        [-t|--test]
        [-v|--verbose]
        [--version]
        [-y|--yes]
        LogicalVolume[Path]


</pre> 


Based on the help info, we run the following command:


<pre>
$ lvreduce -L 358400K /dev/vgdata1vg/lvmyfirstlv
  Rounding size to boundary between physical extents: 352.00 MiB
  WARNING: Reducing active logical volume to 352.00 MiB
  THIS MAY DESTROY YOUR DATA (filesystem etc.)
Do you really want to reduce lvmyfirstlv? [y/n]: y
  Size of logical volume vgdata1vg/lvmyfirstlv changed from 400.00 MiB (100 extents) to 352.00 MiB (88 extents).
  Logical volume lvmyfirstlv successfully resized
</pre>


Now let's confirm that this has worked:



<pre>
$  lsblk
NAME                       MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda                          8:0    0    20G  0 disk
├─sda1                       8:1    0   500M  0 part /boot
└─sda2                       8:2    0  19.5G  0 part
  ├─centos-swap            253:0    0     2G  0 lvm  [SWAP]
  └─centos-root            253:1    0  17.5G  0 lvm  /
sdb                          8:16   0   2.1G  0 disk
├─sdb1                       8:17   0   1.1G  0 part
├─sdb2                       8:18   0   500M  0 part
<strong>│ └─vgdata1vg-lvmyfirstlv  253:2    0   352M  0 lvm
</strong>└─sdb3                       8:19   0 503.6M  0 part
  └─vgdata1vg-lvmysecondlv 253:3    0   464M  0 lvm  /tmp/lvdata2
sr0                         11:0    1  55.4M  0 rom
</pre>

Now let's mount the lv again:


<pre>
$  mount /dev/vgdata1vg/lvmyfirstlv /tmp/lvdata1
</pre>

Now let's check if this has worked:


<pre>
$ df -h
Filesystem                          Size  Used Avail Use% Mounted on
/dev/mapper/centos-root              18G  4.3G   14G  25% /
devtmpfs                            488M     0  488M   0% /dev
tmpfs                               497M   80K  497M   1% /dev/shm
tmpfs                               497M   14M  484M   3% /run
tmpfs                               497M     0  497M   0% /sys/fs/cgroup
/dev/sda1                           497M  143M  354M  29% /boot
/dev/mapper/vgdata1vg-lvmysecondlv  461M   21M  441M   5% /tmp/lvdata2
<strong>/dev/mapper/vgdata1vg-lvmyfirstlv   332M  2.1M  309M   1% /tmp/lvdata1</strong>

</pre>

Success. 


<h3>Handy lvreduce options</h3> 

In a similar vain to lvextend, lvreduce also has a "-r" option for autoresizing the filesystem for us, which means that we don't need to bother with using the resize2fs command. But we still have to unmount the LV before we use the lvreduce command. So in this approach we therefore would run the following command: 


<pre>
$ lvreduce -rL 350M /dev/vgdata1vg/lvmyfirstlv
</pre>