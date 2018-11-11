---
ID: 406
post_title: 'RHCSA &#8211; The Boot Process'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-the-boot-process
published: true
post_date: 2015-06-05 00:00:00
---
http://www.dedoimedo.com/computers/grub.html

http://www.pluralsight.com/courses/red-hat-enterprise-linux-6-booting-runlevels



Here's what happens when you switch on your machine:

<ol>
	<li>BIOS (Basic Input Output System) - this performs POST (Power On Self Test) - This basically checks all the main hardware is responding, e.g. cpu, ram, motherboard,...etc. If everything passes, it then moves on to the next process. BIOS (Basic Input Output System), The BIOS's code actually resides on the motherboard itself, i.e. it is the motherboard's firmware. The BIOS does various things like setting the system clock. However a key it does is that it determines which device to boot from, e.g. network boot, cdrom, hdd,...etc. It is usually the hdd</li>
	<li>BIOS identifies the boot devicece - this is a storage device, where the mbr contains the first part of GRUB 2 along with the partition table. BIOS then hands over the boot process to GRUB 2</li>
	<li>GRUB2 is executed - The purpose of GRUB2 is to load the kernel and initrd. At this point the GRUB menu is temporarily displayed.
<a href="http://codingbee.net/wp-content/uploads/2015/06/tcWzWBw.png"><img src="http://codingbee.net/wp-content/uploads/2015/06/tcWzWBw.png" alt="tcWzWBw" width="635" height="355" class="alignnone size-full wp-image-4520" /></a></li>
	<li>Root filesystem is mounted from the kernel</li>
	<li>Sytemd is started</li>
	<li>Everything else is started up - e.g. starting logging, mounting partitions, starting default services </li>

</ol>






01. The Boot Process

<b>Commands:</b>

Inserttexthere

<b>Config files:</b>

/etc/inittab                       # this file is where you can set the default run-level. This file used to be a place where you entered all kinds of settings, but now is only used for setting the runlevel. The other settings are

# now set elsewhere.

<b>Notes:</b>

The following steps (boot process) takes place during a system start-up:
<ol>
	<li>           <b>BIOS</b><b>initializes - </b>The BIOS does the following:</li>
	<li>           BIOS is short for Basic Input Output System</li>
	<li>           BIOS is installed on the motherboard itself.</li>
	<li>           The BIOS initializes basic peripherals on the system</li>
	<li>           The BIOS sets the system clock</li>
	<li>           The BIOS searches for the boot devices (I think by scanning the MBR in each hdd)</li>
	<li>                Once the BIOS finds the boot device, it then runs the bootloader software (in this case GRUB) that exists on it.</li>
	<li>           After the BIOS has initialized, the <b>BIOS then hands over the boot process to the</b><b>Bootloader</b> (kown as GRUB) <b>- </b>GRUB does the following:</li>
	<li>           The default bootloader for RHEL is called GRUB. GRUB actually exists in the form of 2 software component (aka stages)</li>
	<li>           Stage 1 GRUB - This resides within the MBR, and it’s only purpose to point to the location of the grub.conf file.</li>
	<li>           Stage 2 GRUB - This resides in the /boot partition of the hdd. IT is what generates the GRUB menu.</li>
	<li>           After (stage 1) GRUB starts, it searches it’s config file to identify what the default kernel image is, and then loads it</li>
	<li>           Stage 1 GRUB then hands over control to Stage 2 GRUB which then generates the grub menu for the user.</li>
	<li>           User then selects the OS (or kernel version) to boot-up with (if not the default), and then GRUB loads the given kernel into the RAM as well as the initrd image (initramfs-{kernel-version}.img).</li>
	<li>           GRUB then hands over control of the boot process to the newly loaded linux kernel.</li>
	<li>           After GRUB <b>loads the first Kernel Image into memory, it then hands over control to the kernel - </b>the kernel does the following:</li>
	<li>           The kernel detects what hardware is on the system</li>
	<li>           Once it has detected everything, it then loads the necessary drivers from it’s initial RAM-file-system (initrd image)</li>
	<li>           This ram-file-system contains device drivers, important scripts, and binary utilities…..that aid with the system boot process.</li>
	<li>           initrd is used by kernel as temporary root file system until kernel is booted and the real root file system is mounted.</li>
	<li>           It then mounts the root file system in read only mode.</li>
	<li>                It then starts the “init” process (which should have pid of 1, since it is the very first process that gets started)</li>
	<li>           <b>After the Kernel has started the “init” process, it then hands over control to the init process - </b>init does the following:</li>
	<li>           After init has started it then runs the necessary script which are located in /etc/init</li>
	<li>           Init then reads the /etc/inittab file to determine the default run level.</li>
	<li>           Start the run-level scripts for the default run-level. This will start the various services (you should see various messages coming up...e.g…. “starting sendmail …. OK”)</li>
	<li>           The system services finishes starting up and a <b>login screen is presented</b>.</li>
</ol>
<b>Must survive reboot:</b>

Inserttexthere

<b>Software to install:</b>

Inserttexthere

<b>GUI tool:</b>

Inserttexthere

<b>Book ref:</b>

Inserttexthere

<b>Study guide ref:</b>

Inserttexthere

<b>Need to learn more about:</b>

<a href="http://www.thegeekstuff.com/2011/02/linux-boot-process/">http://www.thegeekstuff.com/2011/02/linux-boot-process/</a>