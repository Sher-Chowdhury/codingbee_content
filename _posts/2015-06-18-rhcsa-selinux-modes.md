---
ID: 420
post_title: SELinux Modes
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-selinux-modes
published: true
post_date: 2015-06-18 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What are the 3 main SELinux modes?"]
- Disabled
- Permissive
- Enforcing
[/toggle]
[toggle title="What are the 2 commands that you can use to view what the current SELinux mode is?"]
$ getenforce
# or 
$ sestatus
[/toggle]
[toggle title="What is the command that displays the default SELinux that is set during machine boot up?"]
$ sestatus
[/toggle]
[toggle title="Which file do you need to edit to change the default SELinux mode?"]
/etc/selinux/config
# the reboot the machine
[/toggle]
[toggle title="What is the command to (non-persistantly) switch to enforcing SELinux mode?"]
$ setenforce enforcing
[/toggle]
[toggle title="What is the command to (non-persistantly) switch to permissive SELinux mode?"]
$ setenforce permissive
[/toggle]
[toggle title="What do you need to do to (persistantly) switch to permissive SELinux mode?"]
# edit the /etc/selinux/config
[/toggle]
[toggle title="What is the command to persistantly disable SELinux mode?"]
Trick question, you can't do it on the command line, you have to edit /etc/selinux/config and then reboot the machine.
[/toggle]
[/accordion]

<hr/>

SELinux can be disabled, or running in either "passive" (i.e. permissive) or enforcing (i.e. active) modes. So before you can start understanding/using SELinux, you need to understand these modes when to use them.  


<h2>SELinux Modes</h2>

SELinux can run in three different operational modes:

<ul>
<li><strong>Disabled</strong> - In this mode, SELinux is switched off. In practice this means that none of the linux objects are labelled, i.e. they don't have a security context, and consequently the policy type is not being used for anything. In this situation the only active security layers are the DAC type security layers, e.g. ugo+rwx. </li>

<li><strong>Permissive</strong> - In this mode, all objects are labelled and a Policy type is being used. However it will only monitor and write SELinux breaches to log files rather than deny access of any object interacting with another object. This log file is located at <code>/var/log/audit/audit.log</code>. In other words SELinux is observing but not blocking anything at all. This means that the log files would show what SELinux would have blocked if it was in enforcing mode.</li>
<li><strong>enforcing</strong> - This does all the same thing as permissive mode, and at also actively deny access when there is a breach.</li>
</ul>



To see which SELinux mode is currently in use, we use the getenforce command:

<pre>
$ whatis getenforce
getenforce (8)       - get the current mode of SELinux

$ getenforce
Permissive
</pre>


Alternatively we can use the sestatus command:

<pre>
$ whatis sestatus
sestatus (8)         - SELinux status tool


$ sestatus
SELinux status:                 enabled
SELinuxfs mount:                /sys/fs/selinux
SELinux root directory:         /etc/selinux
Loaded policy name:             targeted
Current mode:                   permissive
Mode from config file:          enforcing             # this is the mode machine will boot into     
Policy MLS status:              enabled
Policy deny_unknown status:     allowed
Max kernel policy version:      28
</pre>


To switch from permissive mode to enforcing, or vice versa, we can use the setenforce command:


<pre>
$ whatis setenforce
setenforce (8)       - modify the mode SELinux is running in

$ getenforce
Permissive

$ setenforce enforcing

$ getenforce
Enforcing
</pre> 



Changing the mode using setenforce will not survive a reboot, as indicated by the sestatus command:


<pre>
$ sestatus
SELinux status:                 enabled
SELinuxfs mount:                /sys/fs/selinux
SELinux root directory:         /etc/selinux
Loaded policy name:             targeted
Current mode:                   enforcing
<strong>Mode from config file:          permissive</strong>
Policy MLS status:              enabled
Policy deny_unknown status:     allowed
Max kernel policy version:      28


</pre>



So to make the change permenant, we edit the following file:


<pre>
$ cat /etc/selinux/config

# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
<strong>SELINUX=enforcing</strong>
# SELINUXTYPE= can take one of these two values:
#     targeted - Targeted processes are protected,
#     minimum - Modification of targeted policy. Only selected processes are protected.
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted
</pre>

Note: we will cover <a href="http://codingbee.net/tutorials/rhcsa/rhcsa-selinux-policy-types-and-selinux-attributes/">SELINUXTYPE</a> later. 



<h2>Disabling SELinux</h2>

You can't use the setenforce command for switching to/away-from the disabled mode. Instead that is done by editing the <code>/etc/selinux/config</code> file, and then rebooting the machine. 

While in disabled mode, any objects that are created won't automatically get labelled. Here's an example of what happends

<pre>
$ cat /etc/selinux/config  | grep "^SELINUX="
SELINUX=disabled

# then reboot the machine, after which we then do:

$ getenforce
Disabled
$ ls -lZ Disabled-testfile.txt
-rw-r--r-- root root <mark>?</mark>                                Disabled-testfile.txt
</pre>

The "?" indicates that this object does not have an SELinux context.

Also when you switch from disabled to permissive/enabled mode, then the reboot can take longer. That's because SELinux scans the whole machine searching for any unlabelled objects (like the one above) and sets a label for them. So that we end up with something like:


<pre>
$ getenforce
Permissive
$ ls -lZ Disabled-testfile.txt
-rw-r--r--. root root <strong>system_u:object_r:admin_home_t:s0</strong> Disabled-testfile.txt
</pre> 





You can also switch SELinux modes by passing in a grub parameter during boot time. you set <code>enforcing=0</code> for permissive, and <code>enforcing=1</code> for enforcing.


Note: at the end of of rhcsa exam you need to always ensure that the machine is left in enforcing mode, unless instructed otherwise.