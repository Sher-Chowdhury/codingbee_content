---
ID: 401
post_title: 'RHCSA &#8211; Linux Kernal Overview'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-linux-kernal-overview
published: true
post_date: 2015-05-23 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is the command to display the version of the kernel running?"]
$ uname -r
[/toggle]
[toggle title="Where are the kernel modules stored?"]
/lib/modules
[/toggle]
[toggle title="What is the command to list all the available kernel files?"]
$ ls -lh /boot | grep vmlinuz
[/toggle]
[toggle title="Which top level folder does the kernel creates during bootup?"]
- /proc  (houses running process's data)
- /sys   (houses system device drivers)
- /dev   (houses file representation of all your devices)
[/toggle]
[toggle title="What is the command to view help info about your high level folders?"]
$ man hier
[/toggle]
[/accordion]

<hr/>


The Linux kernel is the core of your OS. You can find out what is your kernel's version using the uname command:


<pre>
$ uname -r
3.10.0-229.14.1.el7.x86_64
</pre>


As a safety measure, never update an existing kernel, just install a new one. Yum is smart enough to do this for you.

The linux kernel is a<a href="http://www.thegeekstuff.com/2012/01/linux-unix-kernel/">monolithic kernel</a>.

The Linux kernel loads device drivers and filesystem drivers from <em>kernel modules</em>. These modules are loaded dynamically, as and when needed. These kernel modules can be found in the <pre>/lib/modules</pre> directory:

<pre>
$ ls -l /lib/modules
total 8
drwxr-xr-x. 8 root root 4096 Sep 19 20:58 3.10.0-229.14.1.el7.x86_64
drwxr-xr-x. 7 root root 4096 Sep 16 19:31 3.10.0-229.el7.x86_64
</pre>

The kernel itself can be found in the <strong>/boot</strong> folder, here is an example of a kernel file:

<pre>
$ ls -lh /boot | grep vmlinuz
-rwxr-xr-x. 1 root root 4.8M Sep 16 19:31 vmlinuz-0-rescue-bb2262658ee64941afea091071b78f45
-rwxr-xr-x. 1 root root 4.8M Sep 15 16:14 vmlinuz-3.10.0-229.14.1.el7.x86_64
-rwxr-xr-x. 1 root root 4.8M Mar  6  2015 vmlinuz-3.10.0-229.el7.x86_64
</pre>


“vmlinuz” refers to the fact that the Linux kernel is stored in a compressed image file. This gets decompressed during bootup.

During boot-up, the kernel creates the following folders:

<ul>
	<li><code>/proc</code> - This contains running processes and any kernel parameters that can modify
</li>
	<li><code>/sys</code> - This contains the detected system devices and details about their drivers. However system devices are not accessible via this folder, they are accessible via the /dev folder.</li>
	<li><code>/dev</code> - This stores the access points for your devices</li>
</ul>

For help info about these high level directories as well as others, checkout:


<pre>
$ man hier
</pre>