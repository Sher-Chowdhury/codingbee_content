---
ID: 357
post_title: 'RHCSA &#8211; Access Control List (ACL)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-access-control-list-acl
published: true
post_date: 2015-04-11 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="01. Which filesystems support acl by default?"]
The xfs and ext4 filesystem
[/toggle]
[toggle title="02. Where can you find help info for acl?"]
$ man acl
[/toggle]
[toggle title="03. What is the command to give the user 'homer', read+write acl permissions to /tmp/testfile.txt?"]
$ setfacl -m user:homer:rw-  /tmp/testfile.txt
[/toggle]
[toggle title="04. What is the command to check that this has worked?"]
$ ls -l /tmp/testfile.txt
# look for "+" at the very end of the rwx string. 
[/toggle]
[toggle title="05. What is the command to view the special permissions for /tmp/testfile.txt?"]
$ getfacl /tmp/testfile.txt
[/toggle]
[toggle title="06. What is the command to remove all acl permissions from /tmp/testfile.txt for the user, homer?"]
$ setfacl -x u:homer /tmp/testfile.txt
[/toggle]
[toggle title="07. What is the command to remove all acl permissions from /tmp/testfile.txt?"]
$ setfacl <span style='letter-spacing:0.1px'>-</span>-remove-all /tmp/testfile.txt
[/toggle]
[/accordion]

<hr/>
<h2>What is ACL</h2>

Access Control List (ACL), is a feature that add’s even greater permission granularity on top of the standard ugo/rwx controls. ACL offers the following:

<ul>
	<li>an extra granularity of permissions-control. E.g. give a particular user account group/owner/or-other-custom permission levels, even though they fall in "others".</li>
	<li>Allows you to set default ugo+rwx setting for files and folders created in a specific directory. This essentially over-rides umask settings. This is handy when using it on a shared-team-folder, and complements nicely with SGID and Sticky bit. </li>
</ul>


<pre>
$ man acl
</pre>

It is also good practice to enable acl using tunefs. This will mean that acl is enabled on the filesystem itself. hence if hdd is moved to another pc, it will still have acl enabled even if other machine doesn't explicit specify the acl option in the /etc/fstab file. However for the XFS filesystem, the acl option is enabled by default. Which is one reason why XFS is a better alternative to the ext4 filesystem. 

  

<h2>Creating ACL rules for specific user or group</h2>
We use the getacl and setacl commands to manage acl settings for each file/folder:


<pre>
$ whatis getfacl
getfacl (1)          - get file access control lists
$ whatis setfacl
setfacl (1)          - set file access control lists
</pre>


ACL actually inherits standard permissions (ugo/rwx) and uses them as a starting point, and then let's you apply custom exceptions on top of this base.  Before we go any further it is important to realise that ACL is used differently in conjunction to files than to folders. 


<h2>Using ACL on files</h2>

Let's create a file and view it's acl info. We'll create this file as the root user:

<pre>
$ id
uid=0(root) gid=0(root) groups=0(root) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
</pre>

Now let's create this file:

<pre>
$ touch /tmp/testfile.txt
$ ls -l /tmp/testfile.txt
-rw-r--r--. 1 root root 0 Apr 11 17:18 testfile.txt
</pre>

Now let's check this file's acl info:

<pre>
$ getfacl /tmp/testfile.txt
# file: testfile.txt
# owner: root
# group: root
user::rw-
group::r--
other::r--
</pre>

As you can see, getacl starts off by showing the standard rwx/ugo info in long format. ACL inherits the rwx/ugo settings in order to build on them. Now let's say we also want the user "homer" to also have write permission to this file, but no one else. At the moment, homer falls in other, and hence can't write to this file:


<pre>
[homer@localhost scripts]$ echo "hello world" > /tmp/testfile.txt
-bash: testfile.txt: Permission denied
</pre>



One approach to achieve this is to create a new group, then add both root and homer into this group, then use chown to change this files group to this new group. This approach is quite tedious and and long winded. The proper way to resolve this is to use setfacl to overlay a custom permission on /tmp/testfile.txt that's specific to homer only:

  
<pre>
[root@localhost scripts]# setfacl -m user:homer:rw-  /tmp/testfile.txt
</pre>

Here we used the (m)odify option to assign "homer", read+write (rw-) privileges for testfile.txt. Let's confirm that this file now has special permissions on top of the normal permissions:

<pre>
$ ls -l /tmp/testfile.txt
-rw-rw-r--<mark>+</mark> 1 root root 12 Apr 11 17:33 testfile.txt
</pre> 

The "ls -l" now shows a "+" at the end of the permission string. This is an indicator that the setacl command has been used on this file to apply special permissions. 

Now let's check what those special permissions actually are:

<pre>
[root@localhost scripts]# getfacl testfile.txt
# file: testfile.txt
# owner: root
# group: root
user::rw-
<strong>user:homer:rw-</strong>
group::r--
mask::rw-
other::r--
</pre>

As you can see a new entry has been added. 


Now homer has write permissions:

<pre>
[homer@localhost ~]$ echo "hello world" > /tmp/testfile.txt
[homer@localhost ~]$ cat /tmp/testfile.txt
hello world
</pre>


<h2>Deleting and Undoing ACL permissions</h2>

You can remove homer's special permissions like this:

<pre>
setfacl -x u:homer /tmp/testfile.txt
</pre>
 

However to delete all special permissions, you do:

<pre>
$ setfacl --remove-all /tmp/testfile.txt
</pre>

Running this command is like not running setfacl on this command in the first place. You can confirm that this has worked by making sure the "+" is no longer displayed:
<pre>
$ ls -l /tmp/testfile.txt
-rw-r--r--. 1 root root 12 Apr 11 17:33 testfile.txt
</pre>

Also you can check like this:

<pre>
$ getfacl /tmp/testfile.txt
# file: testfile.txt
# owner: root
# group: root
user::rw-
group::r--
other::r--
</pre>



<h2>Using setfacl for setting basic permissions</h2>
You can use setfacl instead of using chmod for setting basic ugo+rwx permissions.
This is done by not specifying a a group name or username. For example the following:


<pre>
$ setfacl -m user::rwx /tmp/testfile.txt
</pre>

Is equivalent to:

<pre>
$ chmod u=rwx testfile.txt
</pre>


<h2>Copying acl special permissions from one file to another</h2>
Another thing you can do is copy the acl permisions from one file to another file, this is done like this:

<pre>
$ getfacl /tmp/file1 | setfacl --set-file=<mark>-</mark> /tmp/file2
</pre>

The "-" in --set-file=- means take the standard input