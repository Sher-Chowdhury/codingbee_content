---
ID: 356
post_title: 'RHCSA &#8211; File and Folder Permissions'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-file-and-folder-permissions
published: true
post_date: 2015-04-11 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What file do you need to edit to make umask value persistant?"]
~/.bashrc
[/toggle]
[toggle title="What is the command to display umask values in human friendly format?"]
$ umask -S
[/toggle]
[toggle title="What is the command to make the file /tmp/testfile.txt have no user or group owns it?"]
$ chown nobody:nobody /tmp/testfile.txt
[/toggle]
[/accordion]

<hr/>


<h2>File Permissions</h2>
To understand files and Folder permissions, let's first create a new user, called "tom", switch into that user, then create a testfile:

<pre>
[root@puppetmaster ~]# useradd tom
[root@puppetmaster ~]# su - tom
[tom@puppetmaster ~]$ touch testfile.txt
</pre>

Now let's look at the testfile's properties:

<pre>
[tom@puppetmaster ~]$ ls -l
total 0
-rw-rw-r--. 1 <mark>tom</mark> tom 0 Oct 29 19:41 testfile.txt
</pre>

In Linux when you create a file, the file's creator automatically become's the file's owner as indicated above. 

When you create a new user, a group with the same name as the user's name is created, and then the user is automatically added as a member of this group. This group is called the user's <strong>private group</strong>.   

In Linux when you create a file, that file also has to be owned by a group, and in normal circumstance's the owner's private group, becomes this file's group owner:

<pre>
[tom@puppetmaster ~]$ ls -l
total 0
-rw-rw-r--. 1 tom <mark>tom</mark> 0 Oct 29 19:41 testfile.txt
</pre>
 

the first character indicates the kind of item this is. An "-" indicates that this is a file. A "d" indicates it is an directory, and an "l" indicates it is a symbolic link. In this case it is an file: 

<pre>
[tom@puppetmaster ~]$ ls -l
total 0
<mark>-</mark>rw-rw-r--. 1 tom tom 0 Oct 29 19:41 testfile.txt
</pre>

Now we have three kinds of users that can potentially interact with this file, the file's owner, a member of the file's group owner, and all the other Linux users (which is referred to simply as "other")

The level of interactions, aka permissions, that each of these three types of users can have, is indicated by the following:

<pre>
[tom@puppetmaster ~]$ ls -l
total 0
-<mark>rw-rw-r--</mark>. 1 tom tom 0 Oct 29 19:41 testfile.txt
</pre>

This string of characeters 9 characters is actually 3 sets of 3 characters. Each set indicates the file permission for the file's owner, the file's group, and "other", respectively. 

The first 3 characters shows the owner's permissions:


<pre>
[tom@puppetmaster ~]$ ls -l
total 0
-<mark>rw-</mark>rw-r--. 1 tom tom 0 Oct 29 19:41 testfile.txt
</pre>

Here's what each character means:

 
<ul>
	<li><strong>First character</strong>: This character can either be set to "r" or "-". "r" means that the file's owner has read access. So that they view the files content, with commands like, cat, less, view, vim....etc. If this is set to "-" then it means that the file's owner isn't allowed to view this file. Behind the scenes, the "r" setting is also represented by the number "4"</li>
	<li><strong>Second character</strong>: This character can either be set to "w" or "-". "w" means that the file's owner has write access. So that they can edit the file's content, using utility like vi and vim, or redirects like ">", ">>". If this is set to "-" then it means that the file's owner isn't allowed to edit this file. Behind the scenes, the "w" setting is also represented by the number "2"</li>
	<li><strong>Third character</strong>: This character can either be set to "x" or "-". "x" means that the file's owner has execute permissions. This means that if this file contains executable code, then the user can run this file by just typing it's full path in the command prompt. If this is set to "-", then it means that the file's owner can't execute this file.  Behind the scenes, the "x" setting is also represented by the number "1".</li>
</ul>

In our example the owner permission is "rw-". Since r=4 and w=1, it means you can represent "rw-" by single number, which is the sum of the permission, which in this case is 5 (4+1). Therefore "rw-" behind the scenes is represented by 5. These numbers are referred to as octal values. Here are all the possible octal values:

--- = 0
--x = 1
-w- = 2
-wx = 3
r-- = 4
r-x = 5
rw- = 6
rwx = 7




The next 3 shows the group's permissions:

<pre>
[tom@puppetmaster ~]$ ls -l
total 0
-rw-<mark>rw-</mark>r--. 1 tom tom 0 Oct 29 19:41 testfile.txt
</pre>


and the last three shows the other's permission.   

<pre>
[tom@puppetmaster ~]$ ls -l
total 0
-rw-rw-<mark>r--</mark>. 1 tom tom 0 Oct 29 19:41 testfile.txt
</pre>

These string of 9 characters van be represented in octal form, So for this example, the permissions "rw-rw-r--" can be represented by "664". 





<h2>Folder Permissions</h2>

The "rwx" have slightly different meanings in the context of folders. 

[table id=4 /]

<h2>Default Permissions</h2>

When we created the file, the permissions ended up defaulting too:


<pre>
[tom@puppetmaster ~]$ ls -l
total 0
-rw-rw-r--. 1 tom tom 0 Oct 29 19:41 testfile.txt
</pre>

Which in this case is 664.  

You might think why isn't the default "777" (rwxrwxrwx) so that everyone has full priveleges. That's because that would mean this file would be an executable which could be dangerous if the file contains malicious code. Based on this, then the default should now be, 666 (rwxrwxrwx). However that is not the case either. That's because there is something else that influences the default permission, and that is called the umask value:

<pre>    
[tom@puppetmaster ~]$ umask
0002
</pre>

This value is subtracted from 666 to leave with the default permission, 664

However if we create a folder we'll see that :

<pre>
[tom@puppetmaster ~]$ mkdir testfolder
[tom@puppetmaster ~]$ ls -l
total 0
drwxrwxr-x. 2 tom tom 6 Oct 30 19:21 testfolder
</pre>

Here the folder permission defaults to 775. That's because in the case of folders, it is safe for the starting point to be 777 (rwxrwxrwx), since execute has a different meaning when it comes to folders. There if we subtract the umask value from  777, we end up with 775.  

You can change the umask value, you can do:

<pre>
[tom@puppetmaster ~]$ umask 0007
[tom@puppetmaster ~]$ umask
0007
</pre> 

However this change will only last for the current sessions, to make it persistant, you need to add this command to the end of the following file:


<pre>
~/.bashrc
</pre>


You can also view your default settings in a more human readable form:

<pre>
[tom@puppetmaster ~]$ umask
0007
[tom@puppetmaster ~]$ umask -S
u=rwx,g=rwx,o=
</pre>

Note: the above is in the context of folders. 



<h2>Changing Permissions</h2>

You can change a file's or folder's permission using the <code>chmod</code> command. There chmod command can accept 2 forms of instructions, the first form is by octal values: 

<pre>
[tom@puppetmaster ~]$ ls -l
total 0
-rw-rw----. 1 tom tom 0 Oct 30 20:52 testfile.txt
[tom@puppetmaster ~]$ chmod 777 testfile.txt
[tom@puppetmaster ~]$ ls -l
total 0
-rwxrwxrwx. 1 tom tom 0 Oct 30 20:52 testfile.txt
</pre>

The second form is by human readable notation:

<pre>
[tom@puppetmaster ~]$ chmod ugo-x testfile.txt
[tom@puppetmaster ~]$ ls -l
total 0
-rw-rw-rw-. 1 tom tom 0 Oct 30 20:52 testfile.txt
</pre>

chmod is used exactly the same way for folders as well. However you can recursively change the permissions of all the contents of a particular folder using the "-R" setting:

<pre>
$ chmod -R g+w testfolder 
</pre>

<h2>Changing File and Folders ownerships</h2>

If you are the owner of a particular file, and you want to assign ownership to another user, then you can do this using the <code>chown</code> command: 

<pre>
$ ls -l
total 0
-rw-rw-rw-. 1 tom tom 0 Oct 30 20:52 testfile.txt
$ chown jerry:jerry testfile.txt
$ ls -l
total 0
-rw-rw-rw-. 1 jerry jerry 0 Oct 30 20:52 testfile.txt
</pre>

Here we changed both a file's and a group's ownership using the "{user}:{group}" notation.

If you just want to change the group only, but keep the owner unchanged, then you simply declare the existing owner, e.g., here we just want to change the group to "tom", but keep the owner as jerry, therefore we do:

<pre>
$ ls -l
total 0
-rw-rw-rw-. 1 jerry jerry 0 Oct 30 20:52 testfile.txt
$ chown jerry:tom testfile.txt
$ ls -l
total 0
-rw-rw-rw-. 1 jerry tom 0 Oct 30 20:52 testfile.txt
</pre>

You can also take the same approach of keeping the same group but changing the owner. 

With chown, there are also a special reserved word, <code>nobody</code> which is used to a change the owner or group to "nobody", e.g.:


<pre>
$ ls -l testfile.txt
-rw-r--r--. 1 root root 12 Oct 30 22:10 testfile.txt
$ chown nobody:nobody testfile.txt
$ ls -l testfile.txt
-rw-r--r--. 1 nobody nobody 12 Oct 30 22:10 testfile.txt
</pre>