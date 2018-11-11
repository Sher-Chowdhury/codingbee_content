---
ID: 485
post_title: Applying ACLs to directories
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-applying-acls-to-directories
published: true
post_date: 2015-11-15 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="Lets say you have a folder called /tmp/research-team-folder and you want to give the user jerry, 'rw-' permissions. Note, jerry is not the owner nor one of the group owners of this folder. What are the five steps you need to do achieve this"]
1. Update user+group ownerships using chown (if necessary).
2. Update the normal ugo+rwx permissons using chmod (if necessary).
3. Update suid, sgid, and sticky bit special permissions, if necessary.  
4. Apply ACL settings recursively to all existing child files/folders (using the setfacl command)
5. Set default ACL settings on the folder (using setfacl command)
[/toggle]
[toggle title="What is the command to apply specials permissions to give jerry, 'rw-' to the folder /tmp/research-team-folder and all it's existing child files and folders?"]
$ setfacl -R -m user:jerry:rw<mark>X</mark> /tmp/research-team-folder
[/toggle]
[toggle title="What does the capital 'X' mean?"]
This mean execute permissions for child folder but not on files. 
[/toggle]
[toggle title="What is the command to give jerry, 'rw-' priveleges for any new files+folders that are created in the folder, /tmp/research-team-folder?"]
$ setfacl -m default:user:jerry:rwX /tmp/research-team-folder
[/toggle]
[toggle title="What command do you run copy across acl setting from /tmp/file1 to /tmp/file2?"]
$ getfacl /tmp/file1 | setfacl <span style="letter-spacing:0.1px">-</span>-set-file=- /tmp/file2
[/toggle]
[/accordion]

<hr/>




<h2>Using ACL on directories</h2>


So far we have seen how to give a user/group special permissions for a particular file. However you can also give a user/group special permissions to a folder. This is very common scenario, and for this scenario it is very common to:

<ul>
	<li>Recursivley apply the same special permissions to everything inside this folder</li>
	<li>Set default permissions on the folder, so that all future new files+folders created inside it automatically inherits the same special permissions.</li>
</ul>

To achieve this, we need to take the following steps
<ol>
	<li>First set directory's standard permissions using the chmod and chown command.</li>
	<li>Apply ACL settings recursively to all existing child files/folders</li>
	<li>Set default ACL settings on the folder</li>
</ol>

Now let's take a look at the permissions of the "research-team-folder":


<pre>
$ ls -l /tmp | grep "research-team-folder"
drwxr-xr-x. 2 nobody research  6 Nov 15 22:32 research-team-folder
</pre>

At the moment it doesn't have any special permissons 

<pre>
$ getfacl /tmp/research-team-folder/
getfacl: Removing leading '/' from absolute path names
# file: tmp/research-team-folder/
# owner: nobody
# group: research
user::rwx
group::r-x
other::r-x
</pre>

Let's say we We have a user called "jerry" that isn't a member of the "research" group but we want to give it access to this folder via special permissions.  Now let's apply the acl permission:

<pre>
$ setfacl -R -m user:jerry:rw<mark>X</mark> /tmp/research-team-folder
</pre>

Here we are saying (R)ecursively (m)odify the "user" permission jerry to "rwX" for the research-team-folder. Now let's see what the acl permission have become:

Note: the capital "X" indicates to apply "execute" on child folders only and not child files. 

<pre>
$ getfacl /tmp/research-team-folder
# file: research-team-folder/
# owner: nobody
# group: research
user::--x
group::rwx
<strong>user:jerry:rwx</strong> 
mask::rwx
other::--x
</pre>

All the existing folders+files that exist inside the research-team-folder folder all have the same special permissions assigned them. However any new files+folders created inside research-team-folder won't have any special permissions applied to them.  To overcome this we need to set <strong>default special permissions</strong>. 

Now we apply the default permission

<pre>
$ setfacl -m default:user:jerry:rwx /tmp/research-team-folder
</pre>

Note: "default acls" are something that you can only apply to folders and not files. In fact the concept of default special permissions on files wouldn't make any sense anyway.      

Now we end up with:

<pre>
$ getfacl /tmp/research-team-folder
getfacl: Removing leading '/' from absolute path names
# file: tmp/research-team-folder
# owner: nobody
# group: research
user::rwx
user:jerry:rwx
group::r-x
mask::rwx
other::r-x
default:user::rwx
default:user:jerry:rwx
default:group::r-x
default:mask::rwx
default:other::r-x
</pre>