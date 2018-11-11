---
ID: 330
post_title: >
  Automating CentOS/RHEL 7 installations
  using Kickstart
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/cookbooks/centos/rhcsa-automating-rhel-installations-using-kickstart
published: true
post_date: 2015-03-22 00:00:00
---
<h2>What is kickstart</h2>
When you do a normal installation of a RHEL Operating System, the install process walks you through a series of <a rel="nofollow" href="http://www.tecmint.com/centos-7-installation/">installation screens</a>, prompting you for input such as timezone, language, country....etc. This manual interactive process can get quite tedious if you have to installations on a lot of machines. That's where Kickstart comes to the rescue. Kickstart is used for automating the the RHEL/Centos install process.

The kickstart system lets you feed in a "kickstart" config file into the installation process. This file contains the answers to all the install questions. Therefore the install process will automatically take answers from the kickstart file rather than prompting the user to manually type in the answers. As a result the installation becomes an automated process and doesn't require human interaction. 

By default, a kickstart file is automatically generated when you perform a manual RHEL installation. This kickstart contains the answers that you provided during the manual installation (along with a lot more info). This file is called <strong>anaconda-ks.cfg</strong> and is located in the root user's home directory.  

<pre>
$ ls -l /root/anaconda-ks.cfg
-rw-------. 1 root root 1298 Mar 14 19:24 /root/anaconda-ks.cfg
</pre>

Here's what it looks like:

<pre>
$ cat /root/anaconda-ks.cfg
#version=RHEL7
# System authorization information
auth --enableshadow --passalgo=sha512

# Use CDROM installation media
cdrom
# Run the Setup Agent on first boot
firstboot --enable
ignoredisk --only-use=sda
# Keyboard layouts
keyboard --vckeymap=uk --xlayouts='gb'
# System language
lang en_GB.UTF-8

# Network information
network  --bootproto=dhcp --device=enp0s3 --ipv6=auto --activate
network  --hostname=localhost.localdomain
# Root password
rootpw --iscrypted $6$VN2iccVGyceNgeUG$ieJtErIOc/E/Lfak/DRHNiMm4nVryVqPngCVTztfnSFda/V7BIMCfeKVTAzIbOXQ0JbnHaYwMEdHXEUEjlYP01
# System timezone
timezone Europe/London --isUtc
user --homedir=/home/mchowdhury --name=mchowdhury --password=$6$dfa35FQlLz54y4jU$OrPER9VxI9o/vQQY1kkG2jgEFlk2qVJWtOfqdR3vd/I4NW1rjt.CjO3U0tM8W5JbeOProTYO3PiZ/lg.ufkue/ --iscrypted --gecos="Mirfath Chowdhury"
# X Window System configuration information
xconfig  --startxonboot
# System bootloader configuration
bootloader --location=mbr --boot-drive=sda
autopart --type=lvm
# Partition clearing information
clearpart --none --initlabel

%packages
@base
@core
@desktop-debugging
@dial-up
@directory-client
@fonts
@gnome-desktop
@guest-agents
@guest-desktop-agents
@input-methods
@internet-browser
@java-platform
@multimedia
@network-file-system-client
@print-client
@x11

%end

</pre>

We'll cover more about a creating/editing kickstart files in the next article. But for now we'll cover how to make use of a kickstart file. 
 
<h2>Feeding the Kickstart file into the install process</h2>

Installing Centos using kickstart is a 3 step process

1. Create the kickstart file (if you don't already have one)
2. make the kickstart file available to the boot process (e.g. put kickstart file on a web server)
3. Access the boot prompt during the Centos installation and then tell the boot prompt where your kickstart file is located.


You then go ahead and start the installation process, after a few seconds the very first screen you see during the install process is:

<a href="http://codingbee.net/wp-content/uploads/2015/06/YuvKYPj.png"><img src="https://codingbee.net/wp-content/uploads/2015/06/YuvKYPj.png" alt="" width="578" height="398" class="alignnone size-full wp-image-4518" /></a>

In order to automate the install using kickstart, you press the tab key, which leads to:

<a href="http://codingbee.net/wp-content/uploads/2015/06/tcWzWBw.png"><img src="https://codingbee.net/wp-content/uploads/2015/06/tcWzWBw.png" alt="" width="635" height="355" class="alignnone size-full wp-image-4520" /></a>

We then append the following "ks" setting to the bottom line, which is used for specifying the location of the kickstart file (highlighted in yellow):

<a href="http://codingbee.net/wp-content/uploads/2015/06/tNb9kcg.png"><img src="https://codingbee.net/wp-content/uploads/2015/06/tNb9kcg.png" alt="" width="636" height="357" class="alignnone size-full wp-image-4522" /></a>

Note: You can call the kickstart file anything you like, and even give it any suffix as well. 

Here our kickstart file is stored on a web server, but here are other possible options:

<pre>
ks=http://server/path/to/file  
ks=ftp://server/path/to/file   
ks=nfs:server:/path/to/file    
ks=hd:sda1:/path/to/file 
ks=cdrom:/path/to/file         
</pre>


Now to start the kickstart install:

1. First start your machine and access your BIOS to set the boot order to cd-rom.
2. Place the OS install cd into the cd-rom.
3. Then wait for the Installation boot screen to appear.
4. Hit the tab key (while the first menu option is highlighted). This will display the following line:
=> vmlinuz initrd=initrd.img
5. Append to this line by adding a space followed by one of the following:
ks=http://server/path/to/file        # This is if the file is stored on a web server
ks=ftp://server/path/to/file        # This is if the file is stored on a ftp server
ks=nfs:server:/path/to/file            # This is if the file is stored on a nfs server
ks=hd:device:/path/to/file            # This is if the file is stored on a locally attached hdd.
ks=cdrom:/path/to/file                # This is if the file is stored in a cd.

<h2>Alternative scenario: machine already has centos installed on it</h2>
In this case you don't need change the BIOS settings. Instead wait for the following screen and press enter when prompted. Then type "autoboot" at the prompt and this will take you to the boot screen.

<h2>Alternative scenario: (you want to do a network install instead of using a cd-rom)</h2>
It is actually possible to install centos by accessing the centos iso image via a network instead of using a installation cd. In this scenario, you perform the  <strong>network install</strong> and once you reached the boot installation screen, select tab, and enter the ks entry like before.

This works by the kickstart file containing the url to the iso image.