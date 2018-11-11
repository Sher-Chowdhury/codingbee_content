---
ID: 446
post_title: 'RHCSA &#8211; Autostarting a vm after a reboot'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-autostarting-a-vm-after-a-reboot
published: true
post_date: 2015-09-18 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What tasks do you need to perform to ensure vm starts up at boot time?"]
1. start+enable the libvirtd.service
2. enter into virsh cli, and apply to the autostart setting to the vm
[/toggle]
[toggle title="What is the command to start the virtualisation service?"]
$ systemctl start libvirtd.service
$ systemctl enable libvirtd.service
[/toggle]
[toggle title="What is the command to enable the vm called 'puppetmaster'?"]
$ virsh
virsh # autostart puppetmaster
[/toggle]

[/accordion]

<hr/>



By default when you restart your host machine, then any guest VMs that were running at the time won't automatically start up again after the reboot. 

To make sure that your VMs do start up after a host machine's reboot, you need to do 2 things. First you need to ensure that the libvirtd daemon is enabled, so that it starts up after the reboot. 

<pre>
$ systemctl start libvirtd.service
$ systemctl enable libvirtd.service
</pre>


Then you need to start a virsh interactive session and apply the autostart setting: 

<pre>
# virsh
Welcome to virsh, the virtualisation interactive terminal.

Type:  'help' for help with commands
       'quit' to quit

virsh # list                     # only lists running vms. 
 Id    Name                           State
----------------------------------------------------

virsh # list --all               # lists all vms even those that are not currently running. 
 Id    Name                           State
----------------------------------------------------
 -     testVM                         shut off
virsh # autostart testVM
Domain testVM marked as autostarted
virsh #
</pre>

After this the vm will now autostart after a host machine's reboot.