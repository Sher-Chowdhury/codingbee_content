---
ID: 399
post_title: 'RHCSA &#8211; LVM names and the Device Mapper'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-lvm-names-and-the-device-mapper
published: true
post_date: 2015-05-23 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is the command to list all device mapper devices?"]
$ ls -l /dev | grep "dm-"
[/toggle]
[toggle title="What is the command to list more symbolic links with more meaningful names for the generic dm-* block files?"]
$ ls -l /dev/mapper/

[/toggle]
[toggle title="What is the command to view the alternative symbolic names for lv devices that are created from the vg called 'vgdata1vg'?"]
$ ls -l /dev/vgdata1vg/

[/toggle]
[/accordion]

<hr/>


When it comes to storage devices, The core linux kernal is self contained and doesn't have a direct understanding of LVMs. This means that the kernel can understand things like sda1 and sda2, but not logical volumes. That's why a software layer called "Device Mapper" has been introduced which bridges the this gap between the kernal and LVs. The device mapper allows the kernel to interact with a new type of devices with the naming convention /dev/dm-0, /dev/dm-1, /dev/dm-2...etc. These are "device mapper" devices, and in this case are block files that are used to represent LVs:


<pre>
$ ls -l /dev | grep "dm-"
brw-rw----. 1 root disk    253,   0 May 23 10:59 dm-0
brw-rw----. 1 root disk    253,   1 May 23 10:59 dm-1
brw-rw----. 1 root disk    253,   2 May 23 10:59 dm-2
brw-rw----. 1 root disk    253,   3 May 23 10:59 dm-3
</pre>  

These dm-x block files are created while the system is booting up. That means that these block devices can represent different LVs the next time the machine boots, which is a bit like the same issue we have when dealing with partitions, sda1, sda2,..etc. Furthermore, the names dm-1, dm-2,...etc are quite generic names and don't actually tell which device is which. That's why the Device Mapper offers a more user friendly approach for referring to these block devices, and that is through the use of symbolic links which have more meaningful names. You can find these symbolic links under <code>/dev/mapper</code>, at the moment we have:


<pre>
$ ls -l /dev/mapper/
total 0
lrwxrwxrwx. 1 root root       7 May 23 10:59 centos-root -> ../dm-1
lrwxrwxrwx. 1 root root       7 May 23 10:59 centos-swap -> ../dm-0
crw-------. 1 root root 10, 236 May 23 10:59 control
<strong>lrwxrwxrwx. 1 root root       7 May 23 10:59 vgdata1vg-lvmyfirstlv -> ../dm-2
lrwxrwxrwx. 1 root root       7 May 23 10:59 vgdata1vg-lvmysecondlv -> ../dm-3
</strong>

</pre>

As you can see, the naming convention of a symbolic link that is pointing to the corresponding block file is: {vg-name}-{lv-name}. This approach is makes it much more meaningful and easier for us to work with. 

That's why, it is best practice to prefixes volume group names with "vg", and logical volume names with "lv", so that it makes it even easier to keep track of things. 

These symbolic links are also created at boot time, and the device mapper automatically resolves the symbolic links to the correct dm-x device so that we don't have to worry about them. 


LVM also creates it's own set of symbolic links, in case you don't want to use the Device Manager generated symbolic links. These symbolic links have the structure of <code>/dev/{vg-name}/{lv-name}</code>, so in our example the following folder exists that house the following symbolic links:


<pre>
$ ls -l /dev/vgdata1vg/
total 0
lrwxrwxrwx. 1 root root 7 May 23 10:59 lvmyfirstlv -> ../dm-2
lrwxrwxrwx. 1 root root 7 May 23 10:59 lvmysecondlv -> ../dm-3
</pre>


It really comes to personal preference on which set of symbolic links you want to use when refering to your Logical Volumes. 

One final thing to add is that the device mapper doesn't just handle LVM related stuff, it also handles a lot more, e.g. LUKS encrypted partitions, RAID setups,...etc. Hence the /dev/mapper directly can end up containing a lot of symbolic links.