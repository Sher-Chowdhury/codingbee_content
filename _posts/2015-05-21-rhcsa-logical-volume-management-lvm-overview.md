---
ID: 397
post_title: 'RHCSA &#8211; Logical Volume Management (LVM) Overview'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-logical-volume-management-lvm-overview
published: true
post_date: 2015-05-21 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is the command to list all the available lvm commands along with a short description?"]
$ lvm help
[/toggle]
[/accordion]

<hr/>

LVM is all about creating/managing <em>logical volumes</em>. A logical volume (aka LV), can be thought of as a partition on a "virtual hdd". The storage space of that logical volume can span across several block devices (i.e. actual partitions and hdds). LVs gives you a lot of flexibility to help you manage your various disk space more effectively. It also lets you create any size "virtual partition" that you want, e.g. you can have a 10TB virtual partition (in the form of a LV, which, behind the scenes is made up from several hdds/partitions)

LVM's are a better alternative to using partitions for managing your storage space. Partitions are still necessary for some of the core directories, e.g. the <code>/boot</code> directory needs to have it's own primary partition or hdd. But after that it is better to have all other filesystems running on LVs. 

Here are some of the advantages LVM offers over partitions:

<ul>
	<li>logical volumes are more flexible - LVs can be made up of several hdds/partitions, and you can extend/shrink LVs on the fly.</li>
	<li>easily replace hdds that are nearing the end of their lifetime</li>
	<li>Easily take backups in the form of LVM snapshots</li>
	<li>Easy to add new hdd's to existing LVM setup</li>
	<li>Can create nearly unlimited LVs, i.e. not limited in the same way as with partitions, which you can only have up to 15 partitions per hdd.</li>
</ul>



Assuming that you already have a hdd or partition, then the overall process to creating LVs is as follows:

<ol>
	<li>Create new physical volumes (out of hdd’s and partitions)</li>
	<li>Add physical volumes to a volume group. If no VGs currently exist, then create one.</li>
	<li>Create a logical volume from a volume group.</li>
</ol>


You can treat an LV in the same way that you treat hdds/partitions, i.e. you can install a filesystem on it and mount it. However it has a couple of key differences:

&nbsp;
<ul>
	<li>The mbr won’t store info about LVs</li>
	<li>You can’t mount <code>/boot</code> on a LV, since mbr won’t be able to pass over the boot process to an LV.</li>
</ul>

To create an LV volume, you have to take the following steps:

&nbsp;
<ol>
	<li>Create “Physical Volumes” from block devices. (i.e. partitions and hdds) that have partition-id of 8e (for LVM). These block devices shouldn’t have been formatted (and therefore mounted). The act of creating a pv is like transforming a whole block device into a form of lego.</li>
	<li>Next you add the physical volume to a <em>Volume Group</em>. This is a bit like putting your legos into a lego bucket (i.e. VG), so that they are ready to be used for creating logical-volumes. The legos are often referred to as “physical extents”</li>
	<li>Next you create a “Logical Volume” by pulling out the required disk space from the volume-group.</li>
	<li>The LV can then be used like a partition, i.e. you can install a filestystem (format it) and mount it for use.</li>
</ol>
Note: you cannot install a filesystem on a physical volume or volume group.....they are just intermediary steps that leads to creating logical volumes. This means that physical volumes and volume groups, as standalones....are not that useful.

<h2>LVM commands</h2>


Here's a summary of all the available LVM commands:



<pre>
$ lvm help
  Available lvm commands:
  Use 'lvm help <command>' for more information

  devtypes        Display recognised built-in block device types
  dumpconfig      Dump configuration
  formats         List available metadata formats
  help            Display help for commands
  lvchange        Change the attributes of logical volume(s)
  lvconvert       Change logical volume layout
  lvcreate        Create a logical volume
  lvdisplay       Display information about a logical volume
  lvextend        Add space to a logical volume
  lvmchange       With the device mapper, this is obsolete and does nothing.
  lvmdiskscan     List devices that may be used as physical volumes
  lvmsadc         Collect activity data
  lvmsar          Create activity report
  lvreduce        Reduce the size of a logical volume
  lvremove        Remove logical volume(s) from the system
  lvrename        Rename a logical volume
  lvresize        Resize a logical volume
  lvs             Display information about logical volumes
  lvscan          List all logical volumes in all volume groups
  pvchange        Change attributes of physical volume(s)
  pvresize        Resize physical volume(s)
  pvck            Check the consistency of physical volume(s)
  pvcreate        Initialize physical volume(s) for use by LVM
  pvdata          Display the on-disk metadata for physical volume(s)
  pvdisplay       Display various attributes of physical volume(s)
  pvmove          Move extents from one physical volume to another
  pvremove        Remove LVM label(s) from physical volume(s)
  pvs             Display information about physical volumes
  pvscan          List all physical volumes
  segtypes        List available segment types
  tags            List tags defined on this host
  vgcfgbackup     Backup volume group configuration(s)
  vgcfgrestore    Restore volume group configuration
  vgchange        Change volume group attributes
  vgck            Check the consistency of volume group(s)
  vgconvert       Change volume group metadata format
  vgcreate        Create a volume group
  vgdisplay       Display volume group information
  vgexport        Unregister volume group(s) from the system
  vgextend        Add physical volumes to a volume group
  vgimport        Register exported volume group with system
  vgmerge         Merge volume groups
  vgmknodes       Create the special files for volume group devices in /dev
  vgreduce        Remove physical volume(s) from a volume group
  vgremove        Remove volume group(s)
  vgrename        Rename a volume group
  vgs             Display information about volume groups
  vgscan          Search for all volume groups
  vgsplit         Move physical volumes into a new or existing volume group
  version         Display software and driver version information


</pre>