---
ID: 346
post_title: 'RHCSA &#8211; Adding, modifying, and deleting users'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-adding-modifying-and-deleting-users
published: true
post_date: 2015-04-06 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="01. What is the command to create a user called 'donald'?"]
$ useradd donald
[/toggle]
[toggle title="02. What is the command to view what default settings are applied when creating a new user?"]
$ useradd -D
[/toggle]
[toggle title="03. What is the command to view the donald user's uid+gid values along with correspoding primary+supplementary group names?"]
$ id donald
[/toggle]
[toggle title="04. What is the command to set a password for the user 'donald'?"]
$ passwd donald
[/toggle]
[toggle title="05. Which file stores each user's details?"]
/etc/passwd
[/toggle]
[toggle title="06. Which file stores each user's password in hash form?"]
/etc/shadow
[/toggle]
[toggle title="07. What is the command to edit the donald user account's comment section to 'sitting duck'?"]
$ usermod -c "sitting duck" donald 
[/toggle]
[toggle title="08. What is the command to lock donald's user account?"]
$ usermod -L donald
[/toggle]
[toggle title="09. What is the command to delete the 'donald' user account along with their home directory and mailbox?"]
$ userdel -r donald
[/toggle]
[/accordion]
<hr/>





<h2>Create a new user</h2>

Creating a new user account is done by using the <code>useradd</code> command. Here's the help info for this command:


<pre>

$ useradd --help
Usage: useradd [options] LOGIN
       useradd -D
       useradd -D [options]

Options:
  -b, --base-dir BASE_DIR       base directory for the home directory of the
                                new account
  -c, --comment COMMENT         GECOS field of the new account
  -d, --home-dir HOME_DIR       home directory of the new account
  -D, --defaults                print or change default useradd configuration
  -e, --expiredate EXPIRE_DATE  expiration date of the new account
  -f, --inactive INACTIVE       password inactivity period of the new account
  -g, --gid GROUP               name or ID of the primary group of the new
                                account
  -G, --groups GROUPS           list of supplementary groups of the new
                                account
  -h, --help                    display this help message and exit
  -k, --skel SKEL_DIR           use this alternative skeleton directory
  -K, --key KEY=VALUE           override /etc/login.defs defaults
  -l, --no-log-init             do not add the user to the lastlog and
                                faillog databases
  -m, --create-home             create the user's home directory
  -M, --no-create-home          do not create the user's home directory
  -N, --no-user-group           do not create a group with the same name as
                                the user
  -o, --non-unique              allow to create users with duplicate
                                (non-unique) UID
  -p, --password PASSWORD       encrypted password of the new account
  -r, --system                  create a system account
  -R, --root CHROOT_DIR         directory to chroot into
  -s, --shell SHELL             login shell of the new account
  -u, --uid UID                 user ID of the new account
  -U, --user-group              create a group with the same name as the user
  -Z, --selinux-user SEUSER     use a specific SEUSER for the SELinux user mapping

</pre>

Based on the above info, if you want to take a look at useradd's default settings do:

<pre>
$ useradd -D
GROUP=100
HOME=/home
INACTIVE=-1
EXPIRE=
SHELL=/bin/bash
SKEL=/etc/skel
CREATE_MAIL_SPOOL=yes
</pre>

If you want to adjust these default values, then you can do so by editing the <strong>/etc/login.defs</strong> file and the <code>/etc/default/useradd</code> file. 

These default values means that we don't need to write a really long command to explicitly define these when creating a new user. Therefore to create a new user called Donald, we can simply do:

<pre>
$ useradd donald
</pre>

It's also recommended to use the (c)omment flag (for setting a user's name) when creating a user, e.g.

<pre>
$ useradd -c “donald duck” donald
</pre>


Note, for security we can't pass in the password at this stage of creating a user. We'll cover setting password later on. But let's first check that the user now exist by checking the <code>/etc/passwd</code> file:

<pre>
$ cat /etc/passwd | grep donald
donald:x:1005:1005:donald duck:/home/donald:/bin/bash
</pre>  

In Linux, the <code>/etc/passwd</code> file acts as an official register for all the machine's user accounts. In other words, it's the main place that stores all the information for each user that exists on the machine. 

Another handy way to check whether a user exists is with the <strong>id</strong> command:

<pre>
$ id donald
uid=1005(donald) gid=1005(donald) groups=1005(donald)
</pre>

As you can see here, when you create a new user account, then a group (of the same) is also automatically created and the new user is automatically assigned to that group. This group is referred to as the <a href="http://codingbee.net/tutorials/rhcsa/rhcsa-group-administration/">primary group</a>. Therefore in this example, when we created  case a user called "donald" (using the useradd command), a group of the name "donald" was created automatically, and the new user account "donald" was automatically assigned to the "donald" group.     


<h3>User and Group IDs</h3>
Each user account is automatically assigned with a unique id, which is referred to as <strong>user id</strong> (aka uid). Similarly, each group is assigned with it's own unique id, which is referred to as <strong>group id</strong> (aka gid). 




By default the root user's uid and gid values are both 0 as indicated below:

<pre>
$ cat /etc/passwd | grep "^root"
root:x:0:0:root:/root:/bin/bash
$ id
uid=0(root) gid=0(root) groups=0(root) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
</pre>
Note, the "context" refers to SELinux Security Context, which we'll cover later. 


The uid and gids for internal RedHat "system" user accounts ranges between 1-200. Files/Folders can be owned by these accounts. Also process can be run under these accounts. 

The 201-999 range is reserved for system user accounts that can run processes but don't own any files or folders. 









<h3>Primary and Supplementary Groups</h3>
Any file or folder must be owned by both a user and a group. This is why a user's primary group is so important, when a user create's a new file (or folder) then that user's primary group automatically assumes group ownership of that item. That's why a user must belong to exactly one primary group.   

However users can also be added to other groups as well, and these additional groups are referred to as <a href="http://codingbee.net/tutorials/rhcsa/rhcsa-group-administration/">supplementary groups</a>. 



<h2>Set a password</h2>

If we look at the <code>/etc/shadow</code> file for our new user:


<pre>
$ cat /etc/shadow | grep donald
donald:!!:16716:0:99999:7:::
</pre>

You'll see "!!". This indicates that a password has not been set yet for this user. So now we set the password for our new account, using the <strong>passwd</strong> command:

<pre>
$ passwd {username}
</pre>

You need to run the passwd as the root user. If you are logged in as a normal user, and just want to change your current user's password, then simply run passwd commands on it's own:

<pre>
$ passwd 
</pre>

Therefore, going back to our donald user, let's set the password:


<pre>
[root@localhost ~]# passwd donald
Changing password for user donald.
New password:
BAD PASSWORD: The password is shorter than 8 characters
Retype new password:
passwd: all authentication tokens updated successfully.
[root@localhost ~]#


</pre>

We may get "BAD PASSWORD" warnings, but since we are logged in as root, we can ignore these error messages.

If we check the shadow file again, we'll see that the "!!" is no longer there:

<pre>
[root@localhost ~]# cat /etc/shadow | grep "donald"
donald:$6$MJmDFGEe$GR/DkhS.ARMxS9LFHwB8yK28X5B7et6d9lQiqSyEE41pgQ3t4yUZSu8lMgR0NFmzU5aOGNC5nrpsIU4NiF1hh/:16531:0:99999:7:::
[root@localhost ~]#
</pre>

It is now replaced by an encrypted hash. 

Note this file also stores <a href="http://codingbee.net/tutorials/rhcsa/rhcsa-password-properties-passwd-and-chage-commands/">password policy</a> configurations, such as password expiry dates. We'll cover this later. 



<h2>Modifying a user account</h2>

To modifying an existing user account, you can simply use the usermod command:

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
<mark>  -L, --lock                    lock the user account</mark>
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


Based on this, to add/change a user account's comment field, we do:


<pre>
$ usermod -c “donald duck” donald 
</pre>

Or to lock the user, we do:

<pre>
$ usermod -L donald
</pre>

This command ends up simply prefixing an "!" at the beginning of the hash in the /etc/shadow file:

<pre>
$ cat /etc/shadow | grep donald
donald:!$6$LtEzIHPi$RTfF8d0R1lYyQVHDClyb/PFtUKdCaEXU4uCEomLql05IEVYF9.uPT1b1z6iWllXPq/q.L2Qw85lNIBFGkNvM/.:16716:0:99999:7:::
</pre>



<h2>Deleting user accounts</h2> 


We can delete users using the <strong>userdel</strong>. However this command doesn't delete a user’s:

<ul>
	<li>home directory</li>
	<li>mailbox</li>
</ul>

 

This is a precautionary measure in case there is something valuable in the user’s home directory or mailbox. However if you want to remove them as well, then do:

<pre>
$ userdel -r donald
</pre>