---
ID: 459
post_title: 'LVM &#8211; Deleting a Physical Volume'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-deleting-a-physical-volume
published: true
post_date: 2015-10-03 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is the command to redistribute all the data out of the physical volume, /dev/sdb1?"]
$ pvmove /dev/sdb1    # this can take a few seconds to run
[/toggle]
[toggle title="What is the command remove the physical volume /dev/sdb1, from the volume group vgdata1vg?"]
$ vgreduce vgdata1vg /dev/sdb1
[/toggle]
[/accordion]

<hr/>

There will be times when you want to decommission a block device (e.g. hdd). To do this you first need to delete any physical volumes that is the hdd houses. One of the big advantages of lvm is that you can delete these physical volumes without needing to unmount any filesystems. 

Let's say we want to remove the the /dev/sdb device and it comprises of only one primary partition (/dev/sdb1) which is being using as physical volume. Lets also assume that this physical volume is assigned to the volume group vgdata1vg. Now before we can delete the physical volume we first have to ensure that the volume group it is assigned to has enough capacity to carry on after losing the physical volume, if not then add a another physical volume to it from another block device.  

After that you have to move date out of the /dev/sdb1 redistribute it to all the other physical volumes in the volume group, this is done by running the following command:

<pre>
$ pvmove /dev/sdb1    # this can take a few seconds to run
</pre>

Now we can remove this physical volume from vgdata1vg by running the following command:

<pre>
$ vgreduce vgdata1vg /dev/sdb1
</pre>