---
ID: 414
post_title: 'GRUB2 &#8211; Resetting the root password on CentOS/RHEL 7'
author: sher
post_excerpt: "If you have forgotten what your CentOS/RHEL system's root password is, then you need to reset your machine's root password. Resetting the root password requires rebooting your CentOS 7 machine, and then edit the GRUB parameters during boot time. "
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-resetting-the-root-password-via-grub2
published: true
post_date: 2018-03-29 00:00:00
---
If you have forgotten what your CentOS/RHEL system's root password is, then you need to reset your machine's root password. Resetting the root password requires rebooting your CentOS 7 machine, and then edit the GRUB parameters during boot time. 


During machine boot, you might think that you would need to edit the <a href="https://codingbee.net/tutorials/rhcsa/rhcsa-the-target-unit">grub parameters just so to enter the rescue/emergency targets</a>. However that approach doesn't work because you still need to enter the root password as part of entering the rescue/emergency targets. 


That's why we take a different approach to reset the root password. When you machine is booting up:

<ol>
	<li>Press down then up arrow keys as soon as the kernel selection menu appears. This will pause the boot process</li>
	<li>press "e" in order to edit your grub parameter settings.</li>
	<li>Scroll down to the <code>linux16</code> line, then press the "end" key to reach the end of that line.</li>
	<li>add <code>rd.break</code> at the end of the "linux16" line.</li>
</ol>


<a href="http://codingbee.net/wp-content/uploads/2015/06/yTg5Po4.png"><img src="https://codingbee.net/wp-content/uploads/2015/06/yTg5Po4.png" alt="rd.break" width="738" height="418" class="alignnone size-full wp-image-4628" /></a>


The rd.break setting instructs the boot process to stop at a specific point during the initramfs process. This is a point where just the main core filesystem has been mounted, but none of the other filesystems have been mounted. 


Tip: also good idea to remove the "rhgb quiet" flags from the grub parameter list so that you can monitor what is happening. 


Then do "ctrl+x" (as prompted on the screen), to resume the boot process with the modified grub parameter input. 

After that you'll see:


<a href="http://codingbee.net/wp-content/uploads/2015/06/rtNViYv.png"><img src="https://codingbee.net/wp-content/uploads/2015/06/rtNViYv.png" alt="" width="739" height="420" class="alignnone size-full wp-image-4631" /></a>


At this stage, we've managed to access a terminal before the system fully booted up. Also at this very early stage of the boot process we have the following situation:

<ul>
	<li>The root directory isn't '/' as you would normally expect, instead it's '/sysroot'</li>
	<li>The '/sysroot' directory is sitting on a filesystem that is readonly</li>
</ul>



So first we need to make the root filesystem writable:


<pre>
switch_root:/# mount -o remount,rw /sysroot
</pre>




Next we need to tell our system to treat '/sysroot' as our root directory:

<pre>
switch_root:/# chroot /sysroot
</pre>

This has the affect of changing the command prompt to:

<a href="http://codingbee.net/wp-content/uploads/2015/06/1t1CPgR.png"><img src="https://codingbee.net/wp-content/uploads/2015/06/1t1CPgR.png" alt="" width="735" height="414" class="alignnone size-full wp-image-4633" /></a>

Now we can set the new password by running the following command:


<pre>
sh-4.2# echo NewPassword | passwd --stdin root
</pre>

Here my new root password will become "NewPassword"

Note: rather than running the above command. You can instead use the passwd command in the normal way. 

These commands we've ran so far may have made changes to the machine (e.g. created/deleted files behind the scenes) without SELinux knowing about them, so<a href="http://codingbee.net/tutorials/rhcsa/rhcsa-editing-a-files-security-context"> we need tell SELinux to do a restorecon of the entire machine during the next boot</a>, we do this by creating the following file:


<pre>
sh-4.2# touch /.autorelabel
</pre>
 
Now we exit out of chroot:


<pre>
$ exit
</pre>


You should now see something like this:


<a href="http://codingbee.net/wp-content/uploads/2015/06/BYIOeSA.png"><img src="https://codingbee.net/wp-content/uploads/2015/06/BYIOeSA.png" alt="" width="744" height="423" class="alignnone size-full wp-image-4635" /></a>

Then exit again:

<pre>
$ exit
</pre>

This will end up rebooting the system. <strong>The reboot may take about 2 minutes, which is normal</strong>. That is why you should remove the "rhgb quiet" as suggested so that you can monitor the boot process. Now the machine should reboot and you should then be able to login using your root user's new password. 


See also:

https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/System_Administrators_Guide/sec-Terminal_Menu_Editing_During_Boot.html#proc-Resetting_the_Root_Password_Using_rd.break


[post-content post_name=rhsca-quiz]

[accordion]
[toggle title="Assume you don't have the root password and you reboot your machine. What is the first thing you need to do while the machine is booting up?"]
Wait until the grub menu appears and then hit either up/down arrow, in order to disable the timeout. 
[/toggle]
[toggle title="Then what do you do?"]
press "e"
[/toggle]
[toggle title="Then what do you do?"]
Scroll down to the line begining with "linux16"
[/toggle]
[toggle title="Then what do you do?"]
Hit the "end" key
optional: delete the "rhgb quiet" in order to enable the verbose mode.
[/toggle]
[toggle title="Then what do you do?"]
type "rd.break"
[/toggle]
[toggle title="Then what do you do?"]
then press "ctrl+x" 
[/toggle]
[toggle title="You are now logged. Then what command do you run?"]
$ mount -o remount,rw /sysroot
[/toggle]
[toggle title="Then what command do you run?"]
$ chroot /sysroot
[/toggle]
[toggle title="Then what command do you run?"]
$ echo NewPassword | passwd <span style="letter-spacing:1px">-</span>-stdin root 
[/toggle]
[toggle title="Then what command do you run?"]
$ touch /.autorelabel
[/toggle]
[toggle title="Then what command do you run?"]
# run exit twice
$ exit
$ exit
[/toggle]
[toggle title="Then what do you do?"]
Wait for about 2 minutes
[/toggle]
[toggle title="Then what do you do?"]
The machine will have restarted, now you can try logging in as root with the new password. 
[/toggle]
[/accordion]