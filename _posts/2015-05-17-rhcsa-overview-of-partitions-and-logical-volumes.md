---
ID: 391
post_title: 'RHCSA &#8211; Overview of Partitions and Logical Volumes'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-overview-of-partitions-and-logical-volumes
published: true
post_date: 2015-05-17 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is the command to display all the sata based storage devices attached to your machine?"]
$ ls -l /dev | grep "sd[a-z]$"
[/toggle]
[toggle title="What is the command to display all the ide based storage devices attached to your machine?"]
$ ls -l /dev | grep "hd[a-z]$"
[/toggle]
[toggle title="What is the command to display all the virtual disk based storage devices attached to your machine?"]
$ ls -l /dev | grep "vd[a-z]$"
[/toggle]
[toggle title="Which directories have their own dedicated devices?"]
- /boot
- /
[/toggle]
[/accordion]

<hr/>



To make the most out of your machine's disk storages, it's important that you organize your machine's hdds. There are 2 ways to organize your disk space, they are "partitions", and "Logical Volumes". 

Partitioning is a bit like splitting your hdds in smaller pieces (aka partitions), so that one hdd acts as if it is several smaller hdds. Each partition can then be allocated to a particular part of the linux filesystem.

Partitions and Logical Volumes are similar, but the key difference is that once you create a partition of a certain disk size, you can't easily change it afterwards (without formatting the partition). Whereas with Logical Volumes (aka LVs) you can increase/decrease it's size based on your needs. This makes LVs much more versatile. However LVs have a drawback, which is that a machine can't access a LV's content during the machine's main boot process. Hence in theory if all your machine's hdd are organized into LVs and you're not using any partitions, then your machine would fail to even start up. Hence why partitions are important, since partitions are understood by a machine at a hardware level, whereas LVs are at a software level.   We will cover more about Logical Volumes and LVM later. 

In windows, when you add a new hdd, it appears as a new drive. Hence in a windows machine we have c drive, e drive, f drive,...etc. This means that in windows we have several root directories, one for each hdd. However in Linux, we  just have the one root directory "/" and all the hdds diskpace (whether it is in the form of whole hdds, partitions, or Logical Volumes) can be attached to various parts to this overall hierarchy to add more disk space for certain directories. more about this later. 

Every hardware component of a machine is referred to in Linux by a file, this includes each of the machine's hdd. These files all resides in the <code>/dev</code> directory, aka the system (dev)ices directory. All files that represents a hdd, have the file naming convention of "sda", "sdb", "sdc"....etc. Hence to view list of all hdd's we can do:


<pre>
$ ls -l /dev | grep "sd[a-z]$"
brw-rw----. 1 root disk      8,   0 May 17 11:58 sda
</pre>

Note: I think "sd" stands for "sata disk". There are others to, e.g. hda, which refers to IDE hdds, and vda which refers to virtual disks. 


In the above example, our machine only has one hdd. 


The files in /dev are not designed to be interacted directly. But we often view the content of this directory to understand our machine's makeup.


Similarly, partitions are also represented with the same file name convention, but with a digit suffix:
 

<pre>
$ ls -l /dev | grep "sd[a-z]"
brw-rw----. 1 root disk      8,   0 May 17 11:58 sda
brw-rw----. 1 root disk      8,   1 May 17 11:58 sda1
brw-rw----. 1 root disk      8,   2 May 17 11:58 sda2
</pre>

In this instance, the sda hdd has been organised into 2 partitions. 

If we only had “sda” and no numbers, then it means that the “a” hdd hasn’t been partitioned.

Note: IDE hdds are represented by hda, hdb,…..etc. and partitions being hda1, hda2….etc
Note: virtual disks are represented by vda, vdb,…..etc. and partitions being vda1, vda2….etc.


There are some directories, such as <code>/boot</code> that needs it's own dedicated partition in order for the machine to run effectively. Alternatively we can allocate a whole hdd to <code>/boot</code> instead of a partition, but if that hdd's size is 1TB, and <code>/boot</code> only needs 5GB of disk space at most, then all that extra diskspace would not get used and will go to waste. 


Here's a list of common directories that usually have their own dedicated partitions:


<ul>
	<li><code>/boot</code> - this is used heavily during machine boot.</li>
	<li><code>/</code> - While this is at the top level, it doesn't cover directories that have their own dedicated partition/hdd/logical-volume</li>
</ul>