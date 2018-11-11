---
ID: 348
post_title: 'RHCSA &#8211; Creating, modifying, and deleting groups'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-creating-modifying-and-deleting-groups
published: true
post_date: 2015-04-06 00:00:00
---
<h2>Managing groups overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is the command to display the 'donald' user's uid, gids, primary, and all supplementary groups?"]
$ id donald
[/toggle]
[toggle title="What's the command to view the primary+secondary groups that the user donald belongs to?"]
$ groups donald
[/toggle]
[toggle title="Which file acts as a register that lists all the available groups along with a comma separated list of all users that belong to each group?"]
/etc/group
[/toggle]
[toggle title="What's the command to create the group 'sales'?"]
$ groupadd sales
[/toggle]
[toggle title="What's the command to delete the group 'sales'?"]
$ groupdel sales
[/toggle]
[toggle title="What's the command to add the 'donald' user to the 'sales' group?"]
$ usermod  -aG sales donald
[/toggle]
[toggle title="What's the (gpasswd) command to remove the donald user from the sales group?"]
$ gpasswd -d donald sales
[/toggle]
[toggle title="What's the command to change the donald user's primary group to 'sales'?"]
$ usermod -g sales donald
[/toggle]
[toggle title="What are the commands to temporarily change donald's primary group to the 'sales' group?"]
$ su - donald
$ newgrp sales
$ groups
sales donald
[/toggle]
[toggle title="What is the command to back out of a temparary group?"]
$ exit   # this just exits the group but not the terminal session.
[/toggle]
[toggle title="What's the command to rename the 'sales' group to 'retail'?"]
$ groupmod -n retail sales
[/toggle]
[toggle title="What's the command to make the 'donald' user the group administrator for the group 'sales'?"]
$ gpasswd -A donald sales 
[/toggle]
[toggle title="What's the command that the group administrator, 'donald' can use to add the user goofy to the group 'sales'?"]
$ gpasswd -a goofy sales
[/toggle]
[/accordion]

<hr/>




<h2>Types of Groups</h2>

When you create a new user account, then a group (of the same) is also automatically created and the new user is automatically assigned to that group. This group is referred to as the <strong>primary group</strong>. Therefore when we create a user called "donald" (using the useradd command), a group called "donald" is then created automatically, and the new user "donald" is automatically assigned to the new "donald" group.     

The main reason for this because all files and folders created on a Linux machine must be owned by both a user and a group. This is why a user's primary group is so important, when a user create's a new file (or folder) then that user's primary group automatically assumes group ownership of that item. That's why a user must belong to exactly one primary group. A user's primary group is also known as a private-group (since only that user is a member of that group). 
  
However users can also be added to other groups as well, and these additional groups are referred to as <strong>supplementary groups</strong>. 

Having supplementary groups is useful. For example you can end up with lots of user accounts. One of things you will want to do is organize all these user accounts into various groups. For example you may have a several users who work in your company's sales department in which case you want to create a group called "sales" and add each user to that group. 


You can use the <code>id</code> to view what primary and supplementary groups a user belongs to. For example, for a user called "donald", we do:

<pre>
$ id donald
uid=1001(donald) gid=1001(donald) groups=1001(donald),1002(sales),1003(disney)
</pre>

Here we can see that donald's primary group is "donald", and this user also belongs to 2 supplementary groups, "sales" and "disney". You can also view this info using the <code>groups</code> command:

<pre>
$ groups donald
donald : donald sales disney
</pre>

Note: By default, the first group in the list is the primary group. 

The <code>/etc/group</code> is the main file that stores all the information about all primary and supplementary groups that exist on the machine:

<pre>
$ cat /etc/group
root:x:0:
bin:x:1:
daemon:x:2:
sys:x:3:
adm:x:4:
wheel:x:8:vagrant,mickey
.
.
...etc. 
</pre>


A new entry is added to this file each time a new primary or supplementary group is created. There are three fields per entry, they are, group name, group password, group id, and a comma seperated list of all the users that belong to the group (except for the group's primary user). For more info about this file, see: 

<pre>
$ man 5 group
</pre>




<h2>Creating and Deleting groups</h2>
 
Primary groups are created automatically when we create a new user. Therefore in practice, we usually need to only create supplementary groups. To create a new group, we use the <strong>groupadd</strong> command:


<pre>
$ groupadd sales
$ grep sales /etc/group
sales:x:1006:
</pre>

Similarly to delete a group we do:

<pre>
$ groupdel sales
$ grep sales /etc/group
$
</pre>




<h2>Add user to a group</h2> 

To add a user to a (supplementary) group, you use the <strong>usermod</strong> command:


<pre>
$ usermod --help
Usage: usermod [options] LOGIN

Options:
  -c, --comment COMMENT         new value of the GECOS field
  -d, --home HOME_DIR           new home directory for the user account
  -e, --expiredate EXPIRE_DATE  set account expiration date to EXPIRE_DATE
  -f, --inactive INACTIVE       set password inactive after expiration
                                to INACTIVE
  -g, --gid GROUP               force use GROUP as new primary group
  -G, --groups GROUPS           new list of supplementary GROUPS
  -a, --append                  append the user to the supplemental GROUPS
                                mentioned by the -G option without removing
                                him/her from other groups
  -h, --help                    display this help message and exit
  -l, --login NEW_LOGIN         new value of the login name
  -L, --lock                    lock the user account
  -m, --move-home               move contents of the home directory to the
                                new location (use only with -d)
  -o, --non-unique              allow using duplicate (non-unique) UID
  -p, --password PASSWORD       use encrypted password for the new password
  -R, --root CHROOT_DIR         directory to chroot into
  -s, --shell SHELL             new login shell for the user account
  -u, --uid UID                 new UID for the user account
  -U, --unlock                  unlock the user account
  -Z, --selinux-user SEUSER     new SELinux user mapping for the user account

</pre>




For example to add the user "donald" to the "sales" group, we do:


<pre>
$ usermod  -aG sales donald
</pre>

Now we use "id" commmand to confirm that donald is now a member of that group:

<pre>
$ id donald
uid=1005(donald) gid=1005(donald) groups=1005(donald),1006(sales)
</pre>


Note: the group id (gid) field shows what the primary group is, whereas the group fields shows an array of all groups (primary and supplementary groups) that the user belongs to. 


However to remove a user from a group, you can use the usermod command again, bit it's more fiddly, isntead it's easier to remove a user from a group suing the gpassswd command:

<pre>
$ gpasswd -d goofy disney
Removing user goofy from group disney
</pre>

We'll cover more about the gpasswd command later. 

If you want, you can view the "group" file to see who are members of a given group:

<pre>
$ grep sales /etc/group
</pre>


If you want to remove a user from all supplementary groups then you do:

<pre>
$ id donald
uid=1005(donald) gid=1005(donald) groups=1005(donald),1006(sales),1007(sales1)
$ usermod -G ""  donald
$ id donald
uid=1005(donald) gid=1005(donald) groups=1005(donald)
</pre>




<h2>Setting primary group</h2>
When a user creates a file, it automatically gets a group ownership assigned to it. This is usually the user's private group. That's because by default the private group is also the user's primary group. However if you want to change that, e.g. change it to the "sales" group then you can use the "-g" option. For example let's change the donald user's primary group to "sales":


<pre>
$ ls -l /home/donald
total 0
-rw-rw-r--. 1 donald donald 0 Apr  9 21:18 testfile
$ id donald
uid=1005(donald) gid=1005(donald) groups=1005(donald),1006(sales)
$ usermod  -g sales donald
$ id donald
uid=1005(donald) gid=1006(sales) groups=1006(sales)
$ ls -l /home/donald
total 0
-rw-rw-r--. 1 donald sales 0 Apr  9 21:18 testfile
</pre>

Notice that any files that were previously owned by the donald private group (since they were created by the "donald" user) have automatically switched to the new primary group!!! 

Note, changing the primary group using usermod is persitent, where is using the newgrp command isn't persistent. We'll cover newgrp next. 

<h2>temporary change primary group</h2>
Sometime you may want to change your primary group temporarily. This can be done using the <strong>newgrp</strong> command:

<pre>
[donald@localhost ~]$ id
uid=1005(donald) gid=1005(donald) groups=1005(donald),1006(sales) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
[donald@localhost ~]$ newgrp sales
[donald@localhost ~]$ id
uid=1005(donald) gid=1006(sales) groups=1005(donald),1006(sales) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
[donald@localhost ~]$ exit
exit
[donald@localhost ~]$ id
uid=1005(donald) gid=1005(donald) groups=1005(donald),1006(sales) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023

</pre>

The newgrp is kind of similar to the way the su command works, but instead it is for switching groups. This means that when you use this command, you actually go inside another nested bash shell, with your new primary group. So when you "exit", in effect this resets your primary group to it's default. Therefore you can think of the primary group as which group "your logged into".  



<h2>Rename a group</h2>

To rename a group, you need use the <code>groupmod</code> command:

<pre>
$ groupmod --help
Usage: groupmod [options] GROUP

Options:
  -g, --gid GID                 change the group ID to GID
  -h, --help                    display this help message and exit
  -n, --new-name NEW_GROUP      change the name to NEW_GROUP
  -o, --non-unique              allow to use a duplicate (non-unique) GID
  -p, --password PASSWORD       change the password to this (encrypted)
                                PASSWORD
  -R, --root CHROOT_DIR         directory to chroot into


</pre>


For example if we have a group called "sales" and we want to change it's name to "retail", then we do:

<pre>
$ groupmod -n retail sales
</pre>

All users that belonged to the "sales" group will now belong to the "retail" group instead. 


<h2>Setup group administrators</h2>


You can set up users as group admins for a given group. Group-admins are users who can add/remove members from a group. This is done using the gpasswd command:


<pre>
$ gpasswd --help
Usage: gpasswd [option] GROUP

Options:
  -a, --add USER                add USER to GROUP
  -d, --delete USER             remove USER from GROUP
  -h, --help                    display this help message and exit
  -Q, --root CHROOT_DIR         directory to chroot into
  -r, --delete-password         remove the GROUP's password
  -R, --restrict                restrict access to GROUP to its members
  -M, --members USER,...        set the list of members of GROUP
  -A, --administrators ADMIN,...
                                set the list of administrators for GROUP
Except for the -A and -M options, the options cannot be combined.

</pre>


Let's say we have a group called 'disney', so to view a list of members for this group, we can do:

<pre>
$ cat /etc/group | grep disney
disney:x:1005:donald,minnie
$ cat /etc/gshadow | grep disney
disney:!::donald,minnie
</pre>

Now let's say we want to make the user "mickey" a group administrator for the disney group, then we do:

<pre>
$ gpasswd -A mickey disney
</pre>

Note, only the root user can use the “A” option when running this command. 

Now 'let's chekc that this has worked:

<pre>
$ cat /etc/group | grep disney
disney:x:1005:donald,minnie
$ cat /etc/gshadow | grep disney
disney:!:mickey:donald,minnie
$ id mickey
uid=1003(mickey) gid=1004(mickey) groups=1004(mickey)
</pre>

As you can see, the user mickey, isn't a member of the group, but his is a group administrator for this group. The group administrator can then add/remove new members to the group:

<pre>
$ su - mickey
[mickey@puppetmaster ~]$ gpasswd -a goofy disney
Adding user goofy to group disney
</pre> 

Next we can confirm that this has worked like this:

<pre>
$ cat /etc/group | grep disney
disney:x:1005:donald,minnie,goofy
$ cat /etc/gshadow | grep disney
disney:!:mickey:donald,minnie,goofy
</pre>

see also:

https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/ch-Managing_Users_and_Groups.html