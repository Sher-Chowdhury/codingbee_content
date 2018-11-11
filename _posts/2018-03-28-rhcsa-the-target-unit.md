---
ID: 451
post_title: 'Systemd &#8211; Running CentOS 7 in different modes using systemd targets'
author: sher
post_excerpt: "You can configure your CentOS system to run in different modes. For example you can run your system in graphical mode, if you want to use it as desktop workstation. Or you can run your system in command line mode, if you are only interested in using your system's bash terminal. You can control which mode to run in using systemd targets."
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-the-target-unit
published: true
post_date: 2018-03-28 00:00:00
---
<h2>What are targets?</h2>
You can configure your CentOS system to run in different modes. For example you can run your system in graphical mode, if you want to use it as desktop workstation. Or you can run your system in command line mode, if you are interested in using your systems bash terminal. You can control which mode to run in using systemd targets. 


A <strong>target</strong> is basically a grouping of resources that you want started/activated when your system is booting up. These resources are called <strong>units</strong> in systemd. There are different types of units, and you can list the different types by running:


<pre>$ systemctl -t help
Available unit types:
<strong>service</strong>
socket
busname
<strong>target</strong>
snapshot
device
mount
automount
swap
timer
path
slice
scope   
</pre>


Notice that targets themselves are a type of unit. The 'service' and 'target' units are the main unit types that you're likely to spend most of your time working with. Each unit is represented in the form of a file, these files are found in the <code>/usr/lib/systemd/system</code>. These unit files names have a suffix that relates to the type of unit they are. For example to list all target units, we do:   

<pre>
$ ls -l /usr/lib/systemd/system | grep "target$"
-rw-r--r--. 1 root root  312 Jul  2  2014 anaconda.target
-rw-r--r--. 1 root root  546 Jun 10  2014 basic.target
-rw-r--r--. 1 root root  379 Jun 10  2014 bluetooth.target
-rw-r--r--. 1 root root  366 Jun 10  2014 cryptsetup.target
lrwxrwxrwx. 1 root root   13 Mar 14 19:16 ctrl-alt-del.target -> reboot.target
lrwxrwxrwx. 1 root root   16 Mar 14 19:16 default.target -> graphical.target
-rw-r--r--. 1 root root  431 Jun 10  2014 emergency.target
-rw-r--r--. 1 root root  440 Jun 10  2014 final.target
-rw-r--r--. 1 root root  460 Jun 10  2014 getty.target
-rw-r--r--. 1 root root  522 Jun 10  2014 graphical.target
.
.
.
...etc.
</pre>

Another way to list all target units is by using the systemctl command: 

<pre>
$ systemctl list-unit-files --type=target
UNIT FILE                  STATE
anaconda.target            static
basic.target               static
bluetooth.target           static
cryptsetup-pre.target      static
cryptsetup.target          static
.
.
...etc  
</pre>


To see which targets are currently active, we do:

<pre>
$ systemctl list-units --type target
UNIT                  LOAD   ACTIVE SUB    DESCRIPTION
basic.target          loaded active active Basic System
cryptsetup.target     loaded active active Encrypted Volumes
getty.target          loaded active active Login Prompts
local-fs-pre.target   loaded active active Local File Systems (Pre)
local-fs.target       loaded active active Local File Systems
multi-user.target     loaded active active Multi-User System
network-online.target loaded active active Network is Online
network-pre.target    loaded active active Network (Pre)
network.target        loaded active active Network
nfs-client.target     loaded active active NFS client services
paths.target          loaded active active Paths
remote-fs-pre.target  loaded active active Remote File Systems (Pre)
remote-fs.target      loaded active active Remote File Systems
slices.target         loaded active active Slices
sockets.target        loaded active active Sockets
sound.target          loaded active active Sound Card
swap.target           loaded active active Swap
sysinit.target        loaded active active System Initialization
timers.target         loaded active active Timers

LOAD   = Reflects whether the unit definition was properly loaded.
ACTIVE = The high-level unit activation state, i.e. generalization of SUB.
SUB    = The low-level unit activation state, values depend on unit type.

19 loaded units listed. Pass --all to see loaded but inactive units, too.

</pre>

As you can see, you can have multiple targets that are active at any one time, since a target is just a collection of other units (which can also include other target units). If a target is active, then it means that all the units that is part of that target group are enabled/running/active. You can view a list of all units for a given target (e.g. multi-user.target) using the systemctl command: 

<pre>
$ systemctl list-dependencies multi-user.target
multi-user.target
● ├─abrt-ccpp.service
● ├─ModemManager.service
● ├─systemd-user-sessions.service
● ├─tuned.service
● ├─vboxadd-service.service
● ├─vboxadd.service
● ├─vmtoolsd.service
● ├─basic.target
● │ ├─alsa-restore.service
● │ ├─alsa-state.service
● │ ├─microcode.service
● │ ├─rhel-autorelabel-mark.service
● │ ├─rhel-autorelabel.service
● │ ├─rhel-configure.service
● │ ├─rhel-dmesg.service
● │ ├─rhel-loadmodules.service
● │ ├─selinux-policy-migrate-local-changes@targeted.service
● │ ├─paths.target
● │ ├─slices.target
● │ │ ├─-.slice
● │ │ └─system.slice
● │ ├─sockets.target
● │ │ ├─avahi-daemon.socket
● │ │ ├─cups.socket
● │ │ ├─dbus.socket
● │ │ ├─dm-event.socket
● │ │ ├─virtlockd.socket
● │ │ └─virtlogd.socket
● │ ├─sysinit.target
● │ │ ├─dev-hugepages.mount
● │ │ ├─dev-mqueue.mount
● │ │ ├─dmraid-activation.service
.
.
...etc
</pre>

Notice that a target can also include other targets. In fact the targets have a pyramid like structure, i.e. the top most target activates other targets, which in turn call on further targets. 


<h2>The Default target</h2>

Due to the pyramid structure of targets, it means that there is a single target at the very top of the pyramid. The top most target that gets started during boot time is called the <strong>default target</strong>. You can view what the default target is by running:

<pre>
$ systemctl get-default
graphical.target
</pre>

In this example, it means that this target will start up the GNOME desktop gui interface during boot time. You can change the default target like this:

<pre>
$ systemctl set-default multi-user.target
rm '/etc/systemd/system/default.target'
ln -s '/usr/lib/systemd/system/multi-user.target' '/etc/systemd/system/default.target'

$ systemctl get-default
multi-user.target
</pre>

Notice that changing the default target is just a case of updating the "default.target" symbolic link behind the scenes. 



<h2>Commonly used targets</h2>

The most commonly used targets (in terms of operational mode) are those that are as follows:

<ul>
	<li><strong>multi-user.target</strong> - Fully operational mode, this is the normal mode but without Gnome desktop UI.</li>
	<li><strong>graphical.target</strong> - Same as multi-user.target but with the Gnome desktop UI enabled.</li>
	<li><strong>rescue.target</strong> - this enters troubleshooting mode. A bit like single user mode I think.</li>
	<li><strong>emergency.target</strong> - this is more minimal version of the rescue.target. </li>
</ul>


<h2>Switching betweeen top-level targets</h2>

There's a few ways to switch between top-level targets. First ways to do this:



<ul>
	<li>Change the default target, and then reboot the machine.</li>
	<li>Use systemctl's "isolate" subcommand (this is non-persistant)</li>
	<li>From the grub menu, hit e, then specify the target (this is non-persistant)</li>
</ul>

<h3>Switching targets using the "isolate" subcommand</h3>

We can switch using the isolate subcommand. The isolate subcommand can only be used with a select few targets:


<pre>$ grep -ir 'AllowIsolate=yes' /usr/lib/systemd/system
/usr/lib/systemd/system/multi-user.target:AllowIsolate=yes
/usr/lib/systemd/system/reboot.target:AllowIsolate=yes
/usr/lib/systemd/system/poweroff.target:AllowIsolate=yes
/usr/lib/systemd/system/rescue.target:AllowIsolate=yes
/usr/lib/systemd/system/emergency.target:AllowIsolate=yes
/usr/lib/systemd/system/graphical.target:AllowIsolate=yes
/usr/lib/systemd/system/halt.target:AllowIsolate=yes
/usr/lib/systemd/system/initrd-switch-root.service:AllowIsolate=yes
/usr/lib/systemd/system/initrd-switch-root.target:AllowIsolate=yes
/usr/lib/systemd/system/initrd.target:AllowIsolate=yes
/usr/lib/systemd/system/kexec.target:AllowIsolate=yes
/usr/lib/systemd/system/system-update.target:AllowIsolate=yes
/usr/lib/systemd/system/anaconda.target:AllowIsolate=yes</pre>

This list includes the 4 commonly used targets. 


So we use the isolate command like this:

:

<pre>
$ systemctl isolate rescue.target
</pre>

<a href="http://codingbee.net/wp-content/uploads/2015/06/reNpfwk1.png"><img src="https://codingbee.net/wp-content/uploads/2015/06/reNpfwk1.png" alt="" width="740" height="418" class="alignnone size-full wp-image-4584" /></a>


You can then check that you are in rescue.target state like this:

<a href="http://codingbee.net/wp-content/uploads/2015/06/Ez5hdu6.png"><img src="https://codingbee.net/wp-content/uploads/2015/06/Ez5hdu6.png" alt="" width="738" height="418" class="alignnone size-full wp-image-4585" /></a>


Notice that we only have a small number of targets active, since we are in rescue mode. Even sshd service is disabled.


We can return back to mult-user.target using the isolate subcommand again:


<pre>
$ systemctl isolate multi-user.target
</pre>

By the way, emergency.target looks like this:


<a href="http://codingbee.net/wp-content/uploads/2015/06/pIqSAEC.png"><img src="https://codingbee.net/wp-content/uploads/2015/06/pIqSAEC.png" alt="" width="744" height="426" class="alignnone size-full wp-image-4587" /></a>


Note: due to emergency mode being barebones. You can't use isolate to switch to another target while being in emergency.target state. So the only way out of the emergency mode is to reboot the system. 





<h3>Switching targets via the grub menu</h3>

This is something that you will commonly do if for some reason you have trouble booting up into graphical or mult-user targets. In which you can use grub to boot into a trouble shooting mode, such as rescue.target or emergency.target. While your machine is booting, as soon as you see the grub menu, select the (e)dit option:


<a href="http://codingbee.net/wp-content/uploads/2015/06/vdsRWtz.png"><img src="https://codingbee.net/wp-content/uploads/2015/06/vdsRWtz.png" alt="" width="739" height="415" class="alignnone size-full wp-image-4589" /></a>



Then navigate to the line that starts with "linux16", and go to the end of the line, and then append the following setting:

<pre>
systemd.unit=rescue.target
</pre>

So that we have:

<a href="http://codingbee.net/wp-content/uploads/2015/06/WIyCkNw.png"><img src="https://codingbee.net/wp-content/uploads/2015/06/WIyCkNw.png" alt="" width="735" height="419" class="alignnone size-full wp-image-4590" /></a>


Then control+x to resume the boot process with the modified settings. 

In this example we have gone into the rescue.target, this means that after that has been done, you can use systemctl's isolate command to return to one of the fully operational normal targets, mult-user or graphical. 


[post-content post_name=rhsca-quiz]

[accordion]
[toggle title="What is the command list all the units that the multi-user.target starts up?"]
$ systemctl list-dependencies multi-user.target
[/toggle]
[toggle title="Which directory do you change the contents of to override the contents of /usr/lib/systemd/system?"]
/etc/systemd/system/
[/toggle]
[toggle title="What is the command to list all the units that are started up when the 'graphical.target' is activated?"]
$ ls -l /usr/lib/systemd/system/graphical.target.wants
[/toggle]
[toggle title="What is the command to list the default target in symbolic link form?"]
$ ls -l /etc/systemd/system/default.target
[/toggle]
[toggle title="What is the main command to display the default target?"]
$ systemctl get-default
[/toggle]
[toggle title="What are the 3 ways to switch to a different target?"]
- The systemctl command's set-default subcommand. This is persistant
- Use systemctl's "isolate" subcommand (non-persistant)
- From the grub menu during machine bootup (non-persistant)
[/toggle]
[toggle title="What is the command to set the 'multi-user.target' as the default target?"]
$ systemctl set-default multi-user.target
[/toggle]
[toggle title="What is the systemctl command to switch to 'rescue.target' target non-persistantly?"]
$ systemctl isolate rescue.target
[/toggle]
[toggle title="How do you switch to the 'multi-user.target' via the grub menu?"]
1. Wait for the grub menu, then hit "e",
2. scroll down to the "linux16" line then go to the end of that line
3. type the following:
systemd.unit=multi-user.target
4. press ctrl+x
[/toggle]
[/accordion]