---
ID: 403
post_title: >
  Configuring Kernel Parameters (aka
  system settings) in CentOS/RHEL 7
author: sher
post_excerpt: |
  The kernel itself also comes with its own settings that can be tweaked, via customizing kernel parameters. There are a wide range of kernel level parameters that gets set during system boot up.
layout: post
permalink: >
  https://codingbee.net/tutorials/rhce/kernel-kernel-parameters-aka-system-settings
published: true
post_date: 2018-03-31 00:00:00
---
The kernel itself also comes with its own settings that can be tweaked, via customizing kernel parameters. There are a wide range of kernel level parameters that gets set during system boot up. All the kernel settings are stored in a large selection of files under the <code>/proc/sys</code> directory. The parameters stored in this directory are often referred to as "<strong>system parameters</strong>". Making changes to files in this folder won't be persistant because the entire /proc folder gets deleted and recreated during a reboot.  

To view the parameter settings, you can instead use the sysctl command:

<pre>
$ sysctl -a 
abi.vsyscall32 = 1
crypto.fips_enabled = 0
debug.exception-trace = 1
debug.kprobes-optimization = 1
dev.cdrom.autoclose = 1
dev.cdrom.autoeject = 0
.
.
.etc
</pre>

Here, the periods are used as delimiters, which you can think of forward slashes to locate the file.  

sysctl can also be used to apply non-persistnant changes to the kernel parameter settings:

<pre>
$ sysctl -a | grep "ip_forward "
net.ipv4.ip_forward = 1
$ sysctl net.ipv4.ip_forward=0
net.ipv4.ip_forward = 0
$ sysctl net.ipv4.ip_forward
net.ipv4.ip_forward = 0
</pre>

This is useful to test your changes before making them persistant.  To make it persistant then you need to add the setting to the <code>/etc/sysctl.conf</code> file:

<pre>
$ cat /etc/sysctl.conf
# sysctl settings are defined through files in
# /usr/lib/sysctl.d/, /run/sysctl.d/, and /etc/sysctl.d/.
#
# Vendors settings live in /usr/lib/sysctl.d/.
# To override a whole file, create a new file with the same in
# /etc/sysctl.d/ and put new settings there. To override
# only specific settings, add a file with a lexically later
# name in /etc/sysctl.d/ and put new settings there.
#
# For more information, see sysctl.conf(5) and sysctl.d(5).

</pre>

For example lets say we want to change the net.ipv4.ip_forward setting, which is currently set to:


<pre>
$ sysctl net.ipv4.ip_forward
net.ipv4.ip_forward = 0
</pre>

We want to enable this feature (i.e. set this to "1") and make it survive a reboot. To do this we can append the line "net.ipv6.conf.all.disable_ipv6 = 0" to /etc/sysctl.conf:

<pre>
$ cat /etc/sysctl.conf
# sysctl settings are defined through files in
# /usr/lib/sysctl.d/, /run/sysctl.d/, and /etc/sysctl.d/.
#
# Vendors settings live in /usr/lib/sysctl.d/.
# To override a whole file, create a new file with the same in
# /etc/sysctl.d/ and put new settings there. To override
# only specific settings, add a file with a lexically later
# name in /etc/sysctl.d/ and put new settings there.
#
# For more information, see sysctl.conf(5) and sysctl.d(5).

net.ipv4.ip_forward = 1
</pre> 

<strong>Tip</strong>: There's also the /etc/sysctl.d folder where you can store persistant settings instead, e.g. we could do something like:

<pre>
[root@target sysctl.d]# echo 'net.ipv4.ip_forward = 1' > /etc/sysctl.d/01-ipv4.conf
[root@target sysctl.d]# ll /etc/sysctl.d/
total 4
-rw-r--r--. 1 root root 24 Mar 31 10:51 01-ipv4.conf
lrwxrwxrwx. 1 root root 14 Feb  2 13:31 99-sysctl.conf -> ../sysctl.conf
</pre>
 
Note, you need to prefix the .conf file's name with 2 digits followed by a hyphen. This is to do with executing the .conf files in a sequence. However the actual file name itself can be whatever you want. Now we need to test that this is persistant. You don't need to reboot the system to test for persistant, instead yoy can use the <code>sysctl --system</code> to effectively mimic a reboot:



<pre>
$ sysctl net.ipv4.ip_forward
net.ipv4.ip_forward = 0


$ <strong>sysctl --system</strong>
* Applying /usr/lib/sysctl.d/00-system.conf ...
* Applying /etc/sysctl.d/01-ipv4.conf ...
net.ipv4.ip_forward = 1
* Applying /usr/lib/sysctl.d/10-default-yama-scope.conf ...
kernel.yama.ptrace_scope = 0
* Applying /usr/lib/sysctl.d/50-default.conf ...
kernel.sysrq = 16
kernel.core_uses_pid = 1
net.ipv4.conf.default.rp_filter = 1
net.ipv4.conf.all.rp_filter = 1
net.ipv4.conf.default.accept_source_route = 0
net.ipv4.conf.all.accept_source_route = 0
net.ipv4.conf.default.promote_secondaries = 1
net.ipv4.conf.all.promote_secondaries = 1
fs.protected_hardlinks = 1
fs.protected_symlinks = 1
* Applying /etc/sysctl.d/99-sysctl.conf ...
* Applying /etc/sysctl.conf ...

$ sysctl net.ipv4.ip_forward
net.ipv4.ip_forward = 1


</pre>
 

Success! 


<h2>Some kernel settings should never be modified</h2>

There are some kernel that should never be modified otherwise they could end up causing unexpected side effects. They are quite easy to identify, as their file don't have write permissions. E.g.:


<pre>
[root@box1 kernel]# pwd
/proc/sys/kernel
[root@box1 kernel]# ll | head
total 0
-rw-r--r--. 1 root root 0 Feb 22 17:34 acct
-rw-r--r--. 1 root root 0 Feb 22 17:34 acpi_video_flags
-rw-r--r--. 1 root root 0 Feb 22 17:34 auto_msgmni
-r--r--r--. 1 root root 0 Feb 22 17:34 bootloader_type
-r--r--r--. 1 root root 0 Feb 22 17:34 bootloader_version       << This should not be edited. 
-rw-------. 1 root root 0 Feb 22 17:34 cad_pid
-r--r--r--. 1 root root 0 Feb 22 17:21 cap_last_cap
-rw-r--r--. 1 root root 0 Feb 22 17:34 compat-log
-rw-r--r--. 1 root root 0 Feb 22 17:34 core_pattern

</pre>


[post-content post_name=rhsca-quiz] 

[accordion]
[toggle title="What is the command to view all the current kernel level settings?"]
$ sysctl -a
[/toggle]
[toggle title="What is the command to view the current setting for the 'net.ipv4.ip_forward' switch?"]
$ sysctl net.ipv4.ip_forward
[/toggle]
[toggle title="how do persistantly change this?"]
# Append the following:
"net.ipv4.ip_forward = 1"
# to the file:
/etc/sysctl.conf
[/toggle]
[toggle title="How do you check that the core kernel setting is now persistant?"]
# reboot the system, or run:
$ sysctl --system      # this reloads all kernel settings
[/toggle]
[/accordion]