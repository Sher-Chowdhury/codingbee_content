---
ID: 349
post_title: 'RHCSA &#8211; Special File and Folder Permissions'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-special-file-and-folder-permissions
published: true
post_date: 2015-04-06 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="01. What is the command to modify permission of /tmp/script1.sh so that any member of of the 'other' group can run this script as if it is being run by the script's owner?"]
$ chmod u+s /tmp/script1.sh
[/toggle]
[toggle title="02. What is the name for this type of permission?"]
Set User ID (SUID). 
It is very unlikely that you will ever need to set this type of permission on a file, but commonly used for applying on folders.
[/toggle]
[toggle title="03. What is the command to check that this has worked?"]
$ ls -l /tmp/script1.sh
# look for "s" under user's execute permission
[/toggle]
[toggle title="04. Alternatively what is the command to alter this script's permission, so to run this script as if it is being run by a member of the script's group-owner?"]
$ chmod g+s /tmp/script1.sh
Again, it is highly unlikely that you will need to set this type of permission. 
[/toggle]
[toggle title="05. What is the command to check that this has worked?"]
$ ls -l /tmp/script1.sh
# look for "s" under group's execute permission
[/toggle]
[toggle title="06. What is this type of permission called?"]
Set Group ID SGID - it is commonly used for folders.
[/toggle]
[toggle title="07. What is the command to create a group called 'accountants'?"]
$ groupadd accountants  
[/toggle]
[toggle title="08. What is the command to assign a user called 'tom' to this new group?"]
$ usermod -aG accountants tom
[/toggle]
[toggle title="09. What is the command to change the /tmp/finance folder's ownernship, so that no user owns it, and it's group ownership is set to 'accountants'?"]
$ chown nobody:accountants /tmp/finance
[/toggle]
[toggle title="10. Now what is the command to ensure only members of the accountants group has full priveleges and no one else has any priveleges at all?"]
$ chmod 070 /tmp/finance
[/toggle]
[toggle title="11. What is the command to apply permissions so that any files+folders that gets created in the '/tmp/finance' folder automatically gets adopted by the 'accountants' group?"]
$ chmod g+s /tmp/finance
[/toggle]
[toggle title="12. What is the command to apply a restriction so that members of the 'accountants' group can't delete files/folders that resides in the /tmp/finance folders, unless they are also the file's owner?"]
$ chmod +t /tmp/finance
[/toggle]
[toggle title="13. What is the command to check that this has worked?"]
$ ls -l /tmp | grep finance
# look for "t" under other's execute permissions
[/toggle]
[toggle title="14. What is this type of permission called?"]
The "sticky bit". It is indicated by a "T"
$ ls -l
d---rws--T. 2 nobody accountants 25 Apr 11 11:26 /tmp/finance
[/toggle]
[toggle title="What is the command?"]

[/toggle]
[toggle title="What is the command?"]

[/toggle]
[/accordion]

<hr/>








There will be times when the standard ugo and rwx permissions don't provide enough flexibility to allow a group of people to work collaboratively. That's why another set of permissions, called "special permissions" are available. Here's an overview of these permissions:


[table id=special-permissions-sticky-bit /]
 

<h2>Set User ID (SUID)</h2>

This permission only makes sense if you apply it to a file that is an executable (e.g. a shell script). 

You can apply this permission with chmod command and the "s" value:

<pre>
$ ls -l
total 4
-rwx---r-x. 1 root root 33 Apr 11 10:24 testscript.sh
$ chmod u+s testscript.sh
$ ls -l
total 4
-rws---r-x. 1 root root 33 Apr 11 10:24 testscript.sh
</pre>
 
The "s" under the user's permission means that if an "other" runs this script, then the script will run with the same level of privileges as who ever is the owner of this file, which in this case is the root user. Hence if an ordinary user runs this executable and this executable attempts to delete folders/files that only root has access to, then it will still be able to delete them. 

Note: the other permission still needs to be minimum of r-X in order for it to be able to run this executable. 

This is what makes this type of permission dangerous, especially if the file's owner is root. In the real world it is extremely unlikely that you will ever need to set this type of permission on an executable. 

The reason this permission type exists in the first place is because some internal system executables requires this permission, here is a list of them:

<pre>
$ find / -perm /4000 2>/dev/null
/usr/bin/fusermount
/usr/bin/su
/usr/bin/chage
/usr/bin/gpasswd
/usr/bin/mount
/usr/bin/newgrp
/usr/bin/chfn
/usr/bin/chsh
/usr/bin/umount
/usr/bin/ksu
/usr/bin/passwd      # this is a good example, e.g. when a user wants thier own password 
/usr/bin/pkexec
/usr/bin/crontab
/usr/bin/Xorg
/usr/bin/at
/usr/bin/staprun
/usr/bin/sudo
/usr/sbin/pam_timestamp_check
/usr/sbin/unix_chkpwd
/usr/sbin/userhelper
/usr/sbin/usernetctl
/usr/sbin/mount.nfs
/usr/lib/polkit-1/polkit-agent-helper-1
/usr/lib64/dbus-1/dbus-daemon-launch-helper
/usr/libexec/spice-gtk-x86_64/spice-client-glib-usb-acl-helper
/usr/libexec/pulse/proximity-helper
/usr/libexec/sssd/krb5_child
/usr/libexec/sssd/ldap_child
/usr/libexec/sssd/selinux_child
/usr/libexec/abrt-action-install-debuginfo-to-abrt-cache
/usr/libexec/qemu-bridge-helper
$
 
</pre>



<h2>Set Group ID (SGID)</h2>

This is a special permission that can be applied to files and folder.

 
<h3>Set Group ID (SGID) for files</h3>

You can apply SGID permission to a file using chmod along with the "s" value being attached to the group setting:

<pre>
$ chmod g+s testscript.sh
$ ls -l
total 4
-rwx--Sr-x. 1 root root 33 Apr 11 10:24 testscript.sh
</pre>  

This pretty much has the same affect as the SUID, but instead of executable being run by an ordinary user with the elevated privileges of the script's owner, it is being run as if run by one of the group's member. 

Therefore once again this type of permission on a file is very dangerous, and in the real world it is extremely unlikely that you will ever need to set this type of permission on an executable.



<h3>Set Group ID (SGID) for folders</h3>


Applying this type of permission to a folder, results in something different happening which actually has real world applications. 

It's best to see this with an example. 


Lets assume we want to set up a “team folder” for team collaboration. For example lets say we want to create a folder “research-team-folder” which is only to be used by the “research” team, So here is an example of this in practice.

First we create a group for all the team members to be added to:

<pre>
$ groupadd research  
</pre>
            
This creates a new group called "research" that all the members of the research team can belong to.


Now let's assign the all the research team's members to the research group:


<pre>
$ usermod -aG {group-name} {username}                                                          
</pre>

You need to do this for each member of the research team.


Next we create the team's folder

<pre>
$ mkdir research-team-folder
</pre>

Now let's change group ownership

<pre>
$ ls -l
total 0
drwxr-xr-x. 2 root root 6 Apr 11 11:04 research-team-folder
$ chown nobody:research research-team-folder
$ ls -l
total 0
drwxr-xr-x. 2 nobody research 6 Apr 11 11:04 research-team-folder
</pre>

Notice we used the special reserved word "nobody" here. This folder is now owned by the "research" group, and no user (u) owns this folder. There is also the "nogroup" special reserved word too. 


Next we change this folder's permisions to:


<pre>
$ chmod 070 research-team-folder
$ ls -l
total 0
d---rwx---. 2 nobody research 6 Apr 11 11:04 research-team-folder
</pre>


This is so that only members of the “research” group can access this folder and modify it’s content.


However at this point we encounter a problem. Each user's default primary group, is the user's respective private group. This means that all folder/file will automatically get adopted by the primary group (and default permissions assigned according to umask). So each time a user creates a file/folder, then they would also have to do at least one of the following in order for collaboration to work:

<ul>
	<li>use chmod to give others (o) rwx permissions</li>
	<li>use chgrp or chown to change file's/folder’s group ownership</li>
</ul>




However there is a better alternative to the above 2 options....which is to apply something that's known as <strong>special permissions</strong>.  

You can apply a special permission to a directory so that any content that is created within the research-team-folder, is automatically adopted by the “research” group, instead of the user’s primary group. That special permission is the SGID permission, which is applied as follows:


<pre>
[root@localhost scripts]# chmod g+s research-team-folder
[root@localhost scripts]# ls -l
total 0
d---rws---. 2 nobody research 6 Apr 11 11:04 research-team-folder
</pre>

Now let's switch to a different user and create a new file in this folder and see which group the newly created file belongs:

<pre>
[root@localhost /]# su - sher
Last login: Sat Apr 11 11:15:53 BST 2015 
[sher@localhost ~]$ id
uid=1002(sher) gid=1002(sher) groups=1002(sher),1008<strong>(research)</strong> context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
[sher@localhost ~]$ cd /tmp/research-team-folder/
[sher@localhost research-team-folder]$ touch testfile.txt
[sher@localhost research-team-folder]$ ls -l
total 0
-rw-rw-r--. 1 sher <strong>research</strong> 0 Apr 11 11:26 testfile.txt
[sher@localhost research-team-folder]$
</pre>

Success!

This is the recommanded way to create a team folder that is used to be for collaboration. It is also what the SGID permission is primarily used for. We should also apply sticky bit permission too, which is covered next. 



<h2>Sticky bit</h2>


Now that we have created a shared team folder for collaboration, it means that users can create files and share it easily with each other by placing the files in the shared team folder. However it also means that team member can also delete each other's files. This is where a special permission called "sticky bit" comes to the rescue.

So in this case, we need to apply the sticky bit permission to our shared team folder, which we do by setting the "T" option with the chmod command:



<pre>
$ ls -l
total 0
d---rws---. 2 nobody research 25 Apr 11 11:26 research-team-folder
$ chmod +t research-team-folder/
$ ls -l
total 0
d---rws--<strong>T</strong>. 2 nobody research 25 Apr 11 11:26 research-team-folder
</pre>
  
Notice the "T", which means sticky bit has been applied. 

Now with this in place it means that a user can delete only the files that they have created inside the collaboration folder, but can't delete anyone else's files. However the collaboration folder's owner can still delete any files though. However in this case we set the folder owner to nobody, so no one has this privilege. In the real world, you may want to set the folder owner to the team's leader, or an administrator. In our example, only the root user can over-ride the sticky bit permission and delete any files. 


<h2>Using octal values to set permissions</h2>

So far we set special permissions using human friendly notation, however you can also use octal values.  


For example for setting SUID, we can do:

<pre>
$ chmod u+s {filename} 
</pre>

or octal equivalent:

<pre>chmod 4xxx {filename} </pre> 

Either of these results in something like this:

<pre>
$ ls -l | grep testfile
-rwsr----x     1 username usergroup                154 Nov 23 11:18 testfile
</pre>

However after experimenting I found that using octal values can get messy and it's better to stick to the human readable notation. 


<h2>Capital letters for permissions</h2>

Sometimes you will see:


<pre>
[root@localhost scripts]# ls -l
total 0
d---rwS--T. 2 nobody research 44 Apr 11 15:33 research-team-folder
</pre>

This happens if there was no "x" just before we applied the special permission. Hence in this example it means that the capital "S" is not sitting on top of an x. In practice this means that a member of the research team cannot cd into this directory and hence cannot create files in this directory:

<pre>
[sher@localhost scripts]$ cd research-team-folder/
-bash: cd: research-team-folder/: Permission denied
[sher@localhost scripts]$
</pre> 
   
Hence having a capital "S" is meaningless and is an indicator to you that something is configured incorrectly and needs to be fixed. 

The capital T also indicates a similar kind of problem. 


<h2>Setting consistent default rwx permisions for ugo</h2>

This can be achieved using <a href="http://codingbee.net/tutorials/rhcsa/rhcsa-access-control-list-acl/" title="RHCSA - Access Control List (ACL)">acl</a>.