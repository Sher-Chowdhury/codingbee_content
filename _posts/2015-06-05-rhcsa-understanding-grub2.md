---
ID: 408
post_title: 'RHCSA &#8211; Understanding GRUB2'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-understanding-grub2
published: true
post_date: 2015-06-05 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is that brief menu called that's displayed briefly during machine bootup, prompting you to select a kernel version?"]
The grub menu
[/toggle]
[toggle title="What is config file you can edit to customize this menu's behaviour, e.g. the timeout period?"]
/etc/default/grub
[/toggle]
[toggle title="What is the directory's content that you can also edit to customize this menu's behaviour, e.g. the timeout period?"]
/etc/grub.d
[/toggle]
[toggle title="What is the command to preview your potential new grub configuration?"]
$ grub2-mkconfig | less
[/toggle]
[toggle title="What is the command to persistantly apply your changes?"]
$ grub2-mkconfig > /boot/grub2/grub.cfg 
[/toggle]
[/accordion]

<hr/>


<h2>The GRUB menu</h2>
The <strong>grub menu</strong> is something that get's displayed briefly when your machine is booting up, and it looks like this:


<a href="http://codingbee.net/wp-content/uploads/2015/06/vdsRWtz.png"><img src="http://codingbee.net/wp-content/uploads/2015/06/vdsRWtz.png" alt="vdsRWtz" width="739" height="415" class="alignnone size-full wp-image-4589" /></a>




A important task you may need to do is access the grub menu to <a href="http://codingbee.net/tutorials/rhcsa/rhcsa-managing-services/#Switching_targets_via_the_grub_menu">edit the GRUB parameters to enter the rescue or emergency target</a>. 



This is useful if you need to access your machine in rescue mdoe or emergency mode (scroll down to line start with "linux16", go to the end of line, type systemd.unit={target-name}.unit).  


<h2>Configuring GRUB</h2>
GRUB is short for the "Grand Unified Bootloader" and it is the very first linux related component that is activated when powering up a machine. 

The main config file for grub is:


<pre>
/boot/grub2/grub.cfg
</pre>

However you're not supposed to edit this file directly. Instead you edit this file indirectly by first editing the following:

<ul>
	<li>/etc/default/grub   # this is a file</li>
	<li>/etc/grub.d</li>
</ul>

Any change here can then be fed into the <code>grub2-mkconfig</code> which will generate output that can be piped into <code>/boot/grub2/grub.cfg</code>. 


First we preview the new configuratons:

$ grub2-mkconfig | less



You can control the GRUB's behaviour via the GRUB2's main config file:

<pre>
$ cat /etc/default/grub
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="rd.lvm.lv=centos/swap crashkernel=auto  rd.lvm.lv=centos/root vconsole.font=latarcyrheb-sun16 vconsole.keymap=uk rhgb quiet"
GRUB_DISABLE_RECOVERY="true"
</pre> 

Here the 2 main settings are, GRUB_TIMEOUT - which sets how long the grub menu is displayed before disappearing, and, GRUB_CMDLINE_LINUX, this lists all the arguements that are passed through the kernel while the machine is booting. Here you can remove the "rhgb quiet" in order to get more verbose output when the machine is loading up. 



Note, you can also create grub config files in the following directory too:

<pre>
$ ls -l /etc/grub.d/
total 64
-rwxr-xr-x. 1 root root  8698 Jun 30  2014 00_header
-rwxr-xr-x. 1 root root  9517 Jun 30  2014 10_linux
-rwxr-xr-x. 1 root root 10275 Jun 30  2014 20_linux_xen
-rwxr-xr-x. 1 root root  2559 Jun 30  2014 20_ppc_terminfo
-rwxr-xr-x. 1 root root 11110 Jun 30  2014 30_os-prober
-rwxr-xr-x. 1 root root   214 Jun 30  2014 40_custom
-rwxr-xr-x. 1 root root   216 Jun 30  2014 41_custom
-rw-r--r--. 1 root root   483 Jun 30  2014 README


</pre>

However it is very unlikely that you will need to do anything in this directory. 

Once you have made any changes to grub configurations, you need to run the following command:


<pre>
$ grub
grub2-bios-setup       grub2-macbless         grub2-mkpasswd-pbkdf2  grub2-render-label
grub2-editenv          grub2-menulst2cfg      grub2-mkrelpath        grub2-script-check
grub2-file             <mark>grub2-mkconfig</mark>         grub2-mkrescue         grub2-set-default
grub2-fstest           grub2-mkfont           grub2-mkstandalone     grub2-sparc64-setup
grub2-glue-efi         grub2-mkimage          grub2-ofpathname       grub2-syslinux2cfg
grub2-install          grub2-mklayout         grub2-probe            grubby
grub2-kbdcomp          grub2-mknetdir         grub2-reboot


</pre>


This command essentially updates the MBR with the new grub configurations, e.g. new timeout value. 


Hence if you made any changes to the main grub config file, then you need to run the grub2-mkconfig command, to get it loaded into the mbr: 

<pre>
$ grub2-mkconfig > /boot/grub2/grub.cfg
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-3.10.0-229.4.2.el7.x86_64
Found initrd image: /boot/initramfs-3.10.0-229.4.2.el7.x86_64.img
Found linux image: /boot/vmlinuz-3.10.0-123.el7.x86_64
Found initrd image: /boot/initramfs-3.10.0-123.el7.x86_64.img
Found linux image: /boot/vmlinuz-0-rescue-3cf91f1bd95c4bbfbccf8ee2b5424021
Found initrd image: /boot/initramfs-0-rescue-3cf91f1bd95c4bbfbccf8ee2b5424021.img
done
</pre>

This will update the main grub.cfg file. This is a file that you're not supposed to update directly. Instead you use grub2-mkconfig to generate an updated file /boot/grub2/grub.cfg for you. 

       

https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/System_Administrators_Guide/ch-Working_with_the_GRUB_2_Boot_Loader.html