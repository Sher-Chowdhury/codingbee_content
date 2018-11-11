---
ID: 3035
post_title: 'NFS &#8211; Set up private group folders'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhce/nfs-set-up-private-group-folders
published: true
post_date: 2018-03-12 17:58:38
---
Let's say you want to setup group folder that's available via nfs. However this time only a particulat Linux group is allowed to have read+write access to this folder.  You can do this by ensuring the exported folder is owned by a group, and then setup the <a href="https://codingbee.net/tutorials/rhcsa/rhcsa-special-file-and-folder-permissions">SGID</a>. You can follow along this article using this <a href="https://github.com/Sher-Chowdhury/NFS-CentOS7-demo">vagrant project on Github</a>. 

We will walk through the following example:


<pre>
+--------------------------+              +--------------------------+
|                          |              |                          |
|       nfs-storage        |              |        nfs-client        |
|     (IP: 10.0.6.10)      |              |                          |
|                          |              |                          |
|                          |              |                          |
|                          |              |                          |
|                          |              |                          |
|   +-----------------+    |              |   +------------------+   |
|   | /nfs/hr_team    |<--------------------->| /mnt/hr_team_nfs |   |
|   +-----------------+    |              |   +------------------+   |
|                          |              |                          |
|                          |              |                          |
|                          |              |                          |
+--------------------------+              +--------------------------+
</pre>


The key thing here is that the Linux group that needs read-write access to the shared folder must exist on both machines. The same is true for the users. 

Let's create a couple of dummy users:

So we run the following on both boxes:
<pre>
[root@nfs-storage ~]# useradd bob
[root@nfs-storage ~]# useradd alice
</pre>

and on the client box:

<pre>
[root@nfs-client ~]# useradd bob
[root@nfs-client ~]# useradd alice
</pre>

Now lets create the group:

<pre>
[root@nfs-storage nfs]# groupadd -g 8000 hr_team
</pre>

Here you have to explicitly specify a gid. That's because the group needs to have identify gid for both boxes. The gid has to be unique, if it isn't then it'll give an error message. Let's now mirror this on the client:

<pre>
[root@nfs-client ~]# groupadd -g 8000 hr_team
</pre>

Now we add both user to the group:

<pre>
[root@nfs-storage nfs]# usermod -aG hr_team bob
[root@nfs-storage nfs]# usermod -aG hr_team alice
[root@nfs-storage nfs]# cat /etc/group | grep 8000
hr_team:x:8000:bob,alice
</pre>

And mirror this on the client:

<pre>
[root@nfs-client ~]# usermod -aG hr_team bob
[root@nfs-client ~]# usermod -aG hr_team alice
[root@nfs-client ~]# cat /etc/group | grep 8000
hr_team:x:8000:bob,alice
</pre>

Now let's create the export:

<pre>
[root@nfs-storage nfs]# mkdir -p /nfs/hr_team
[root@nfs-storage nfs]# ll -dZ /nfs/hr_team/
drwxr-xr-x. nfsnobody hr_team unconfined_u:object_r:default_t:s0 /nfs/hr_team/
</pre>

Next let's sort out the SELinux security context:

<pre>
[root@nfs-storage nfs]# semanage fcontext -a -t public_content_rw_t  "/nfs/hr_team(/.*)?"
[root@nfs-storage nfs]# restorecon -R /nfs/hr_team/
[root@nfs-storage nfs]# ll -dZ /nfs/hr_team/
drwxrwsr-x. nfsnobody hr_team unconfined_u:object_r:public_content_rw_t:s0 /nfs/hr_team/
</pre>

tip: if you forget what the tags are called, run the following for a remindoer:

<pre>
$ seinfo -t | grep public
</pre>
At the moment this folder is owned and group owned by root:

<pre>
[root@nfs-storage nfs]# ll /nfs/
total 0
drwxr-xr-x. 2 root root  6 Mar 12 18:06 hr_team
</pre>


Now let's set this to the new group:


<pre>
[root@nfs-storage nfs]# chown -R nfsnobody:hr_team /nfs/hr_team
[root@nfs-storage nfs]# ll /nfs
total 0
drwxr-xr-x. 2 nfsnobody hr_team  6 Mar 12 18:06 hr_team
</pre>

The <strong>nfsnobody</strong> user is a special reserved username used specifically for this purpose. Now we set the sgid flag:


<pre>
[root@nfs-storage ~]# chmod g+rws hr_team/
[root@nfs-storage nfs]# ll -d /nfs/hr_team/
drwx<strong>rws</strong>r-x. 2 nfsnobody hr_team 6 Mar 12 18:06 /nfs/hr_team/
</pre>

Now we update /etc/exports to contain the following line:

<pre>
[root@nfs-storage nfs]# cat /etc/exports
/nfs/hr_team    *(rw,no_root_squash)
</pre>

Now we restart nfs deamon and check that our new group folder has been exported:

<pre>
[root@nfs-storage nfs]# systemctl restart nfs-server
[root@nfs-storage nfs]# showmount -e localhost
Export list for localhost:
/nfs/hr_team *
</pre>


On the NFS client, we need to create a mount point:


<pre>
[root@nfs-client ~]# mkdir -p /mnt/hr_team_nfs
</pre>

And then add the following entry into the <code>/etc/fstab</code> file:


<pre>
10.0.6.10:/nfs/hr_team   /mnt/hr_team_nfs   nfs    soft,timeo=100,_netdev,rw   0   0
</pre>

Then do a mount all:


<pre>
[root@nfs-client ~]# mount -a
</pre>

Finally we can run <code>mount</code> or <code>findmnt</code> confirm that it has been mounted. Then try creating content as various users. 

Note: uid values of the created users don't matter. It's just the group id that requires matching. 


[post-content post_name=rhsca-quiz] 

[accordion]
[toggle title="What are the steps involved in configuring an nfs export into a private group folder?"]
1. Use the groupadd command to create a group with identical name and gid value on both nfs-server and nfs-clients
2. On the nfs-server, use chown to make this new group the owner of the exported folder. 
3. Use chmod to ensure group has full rwx access to the export folder. But 'other' has no access. 
4. Use chmod to set the sgid permissions on the group. So that newly created content is owned by the right group. 
5. On the client create/add-existing users to the group. 
6. test group users can create files/folders inside the mount point.   
[/toggle]
[toggle title="What is the command to create a group called 'admins' with the gid of 4005?"]
$ groupadd -g 4005 admins
[/toggle]
[toggle title="what is the command to set the sgid on the folder /nfs/hr_team?"]
$ chmod g+s /nfs/hr_team
[/toggle]
[toggle title="question?"]
answer
[/toggle]
[toggle title="question?"]
answer
[/toggle]
[toggle title="question?"]
answer
[/toggle]
[toggle title="question?"]
answer
[/toggle]
[/accordion]