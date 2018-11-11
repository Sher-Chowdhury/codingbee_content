---
ID: 479
post_title: The systemd emergency.target
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-the-emergency-target
published: true
post_date: 2015-11-11 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="How do you switch to the 'emergency.target' via the grub menu?"]
1. (Re)boot the machine
2. Wait for the grub menu to appear, then hit "e",
3. scroll down to the "linux16" line then press the 'end' key
4. type the following:
systemd.unit=emergency.target
5. press ctrl+x
[/toggle]
[toggle title="What is the command to make the root directory wrieable?"]
$ mount -o rw,remount /
[/toggle]
[/accordion]
<hr/>




If you have problems booting up your machine, then you can boot into the emergency.target. In this target you can explore your filesystem for possible issues. 

In this filesystem, the root filesystem (/) has been mounted as root only mode. Therefore before you can make any fixes, then while in the emergency target, you will need to first run the following command:


<pre>
$ mount -o rw,remount /
</pre> 

After that you can edit your config files, e.g. /etc/fstab or /etc/sudoers.