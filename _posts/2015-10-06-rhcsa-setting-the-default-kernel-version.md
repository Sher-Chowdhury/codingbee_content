---
ID: 465
post_title: Setting the default kernel version
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-setting-the-default-kernel-version
published: true
post_date: 2015-10-06 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is the technique to list all your grub related commands?"]
$ grub2-  {tab+tab}
[/toggle]
[toggle title="What is the command to set the kernel to most recent version?"]
$ grub2-set-default 0
[/toggle]
[toggle title="How do you check the above command has worked?"]
# reboot your machine, then run 
$ uname -r
[/toggle]
[/accordion]

<hr/>

By default, when your machine is starting up, it will automatically default to starting up the latest installed kernel. That is your bootloader's default behaviour. 

However there are lot of commands available that can adjust your bootloader's behavior: 

<pre>
$ grub2-  {tab+tab}
grub2-bios-setup       grub2-macbless         grub2-mkpasswd-pbkdf2  grub2-render-label
grub2-editenv          grub2-menulst2cfg      grub2-mkrelpath        grub2-rpm-sort
grub2-file             grub2-mkconfig         grub2-mkrescue         grub2-script-check
grub2-fstest           grub2-mkfont           grub2-mkstandalone     <mark>grub2-set-default</mark>
grub2-glue-efi         grub2-mkimage          grub2-ofpathname       grub2-sparc64-setup
grub2-install          grub2-mklayout         grub2-probe            grub2-syslinux2cfg
grub2-kbdcomp          grub2-mknetdir         grub2-reboot
</pre>

The <strong>grub2-set-default</strong> commands let's you set the default grub version. For example if you run the following:

<pre>
$ grub2-set-default 0
</pre>

This set's the most recent kernel version as the default kernel. 


However if you run:


<pre>
$ grub2-set-default 1
</pre>

Then the second most recent kernel version becomes the default....and so forth and so on. 

Unfortunately this command doesn't let you specify a specific kernel version. So if you want to continue using a particular kernel version, then it means that you have to use this command every time a newer kernel version is installed. 

To confirm that this has worked you can run the <code>uname -r</code> command to check with kernel version is currently running. 


Luckily there is a package that you can use that let's you set specific kernel version.