---
ID: 404
post_title: 'RHCSA &#8211; Update the kernel'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-update-the-kernel
published: true
post_date: 2015-05-28 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="True or false, the older kernel gets uninstalled when you update to the newer kernel?"]
False
[/toggle]
[toggle title="What is the command to list all available kernel versions?"]
$ yum list kernel --showduplicates
[/toggle]
[toggle title="What is the command to update your kernel?"]
$ yum update kernel
[/toggle]
[/accordion]

<hr />

You can update an installed package to it's latest stable version using the <code>yum update {package-name}</code> command. When you use yum to update a package, behind the scenes the old package gets uninstalled and then the newer version gets installed. However that is not the case if you want to update the kernel itself. Since the kernel is the core of the OS, it is too risky to get rid of the existing version of the kernel in case you want to go back to using it again in the future. That's why when you do a kernel update, what ends up happening is that another version of the kernel is also made available on your machine. You can then choose which version of the kernel you want to use during the machine boot time.

When your machine is booting up, the following window is briefly displayed (if you have more than one version of the kernel available on your machine):

<a href="http://codingbee.net/wp-content/uploads/2015/05/available-kernel-versions.png"><img class="alignnone size-full wp-image-4493" src="http://codingbee.net/wp-content/uploads/2015/05/available-kernel-versions.png" alt="" width="720" height="399" /></a>

If you wait a few seconds then the latest kernel version is automatically selected.

Now lets go ahead and install the latest stable version of the kernel, which in my case is:
<pre>
$ yum list kernel --showduplicates
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirror.ox.ac.uk
 * epel: download.fedoraproject.org
 * extras: mirror.as29550.net
 * updates: mirror.ox.ac.uk
Installed Packages
kernel.x86_64            3.10.0-229.14.1.el7        @updates
Available Packages
kernel.x86_64            3.10.0-229.el7             base
kernel.x86_64            3.10.0-229.1.2.el7         updates
kernel.x86_64            3.10.0-229.4.2.el7         updates
kernel.x86_64            3.10.0-229.7.2.el7         updates
kernel.x86_64            3.10.0-229.11.1.el7        updates
kernel.x86_64            3.10.0-229.14.1.el7        updates
</pre>
Here we can see one of the kernel is already installed and a newer version is available.

To update the kernel we use yum's update command:
<pre>
$ yum update kernel
</pre>
This should end up creating a new "vmlinuz" file:
<pre>$ rpm -ql kernel | grep "/boot/vmlinuz"
/boot/vmlinuz-3.10.0-229.el7.x86_64
/boot/vmlinuz-3.10.0-229.14.1.el7.x86_64
</pre>
However if for whatever reason your particular vmlinuz file isn't there, then you can generate one by simply running the <strong>dracut</strong> command:
<pre>$ dracut
$
</pre>
You can just run this command on it's own with no extra options. However it is very unlikely you will ever need to use this command.

Now when I reboot the machine, the new kernel is now available and is also the new default since it is the latest version, and is the first kernel in the list:

<a href="http://codingbee.net/wp-content/uploads/2015/05/4V6kULf.png"><img class="alignnone size-full wp-image-4497" src="http://codingbee.net/wp-content/uploads/2015/05/4V6kULf.png" alt="" width="720" height="400" /></a>

After the machine has booted up, if we then go in and see what kernels are installed, we see:
<pre>
$ yum list kernel
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: anorien.csc.warwick.ac.uk
 * extras: mirror.sov.uk.goscomb.net
 * updates: mirror.ox.ac.uk
Installed Packages
kernel.x86_64          3.10.0-123.el7                @anaconda
kernel.x86_64          3.10.0-229.4.2.el7            @updates
</pre>
This time yum shows that both kernels are now installed. However only one of them is currently running and you can can find out which one using the <code>uname -r</code> command.

You can now switch between these 2 kernels by rebooting your machine and then picking your kernel when prompted during the boot up process.