---
ID: 395
post_title: 'RHCSA &#8211; Encrypted Filesystems'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-encrypted-filesystems
published: true
post_date: 2015-05-21 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What happens when you try to cd into the mountpoint of an encrypted filesystem?"]
You will get prompted to enter a password. 
[/toggle]
[toggle title="What is the command to encrypt /dev/sdb3?"]
$ cryptsetup luksFormat /dev/sdb3
[/toggle]
[toggle title="What is the command to check that an encrypted 'filesytem container' has been installed on /dev/sdb3?"]
$ blkid | grep "^/dev/sdb3"
[/toggle]
[toggle title="What is the command to unlock your /dev/sdb3 give the new uncrypted block device, the name 'decryptedpartition'?"]
$ cryptsetup luksOpen /dev/sdb3 decryptedpartition 
# you will get prompted for a password
[/toggle]
[toggle title="What is the command to check that this filesystem is a child element of /dev/sdb3?"]
$ lsblk
[/toggle]
[toggle title="Where will the new 'decryptedpartition' block device appear?"]
/dev/mapper/decryptedpartition
[/toggle]
[toggle title="What is the command to install the xfs filesystem on 'decryptedpartition'?"]
$ mkfs.xfs /dev/mapper/decryptedpartition
[/toggle]
[toggle title="What is the command to install the 'decryptedpartition' device to the mountpoint '/tmp/secret/?"]
$ mount /dev/mapper/decryptedpartition /tmp/secret
[/toggle]
[toggle title="What is the command to unmount this decrypted partition?"]
$ umount /dev/mapper/decryptedpartition
[/toggle]
[toggle title="What is the command 'log out' of the decrypted partition?"]
$ cryptsetup luksClose /dev/mapper/decryptedpartition
[/toggle]
[/accordion]

<hr/>



In Linux it is possible to encrypt a filesystem so that you get a prompt to enter a password when you try to cd into it's corresponding mountpoint. Hence this is an extra layer of security on top of ugo+rwx and SELinux system. The way it works is that there is an extra step to perform just before installing the actual filesystem. Here's the process to creating an encrypted partition (using /dev/sdb3 as an example):


<ol>
	<li>Create the partition - i.e. <code>fdisk /dev/sdb</code>.</li>
	<li>Run the <code>cryptsetup luksFormat /dev/sdb3</code> command, note the uppercase "F". This will prompt you to choose a new password for your encrypted partition.</li>
	<li>Unlock your encrypted partition using <code>cryptsetup luksOpen /dev/sdb3 decryptedpartition</code>, note the upper case "O". Here we also specify the name we want for the unencrypted partition which in this example is "decryptedpartition". This will prompt you to enter the password that you set earlier. After you enter the correct password, a new block device will appear in /dev/mapper/decryptedpartition</li>
	<li>Format the newly decrypted block device, in the same way as you would do for any other partition, <code>mkfs.xfs /dev/mapper/decryptedpartition</code></li>
	<li>mount your decrypted partition: <code>mount /dev/mapper/decryptedpartition /tmp/secret</code>, make sure you create the mountpoint if it doesn't already exist. You should now be able to start storing files in /tmp/secret and it will gets stored in the encrypted filesystem behind the scense, e.g. <code>touch /tmp/secret/testfile.txt</code></li>
</ol>


Now let's see all this in action:


<strong>1. create a partition using fdisk, or choose an existing partition</strong>
In my case I will use the following existing partition:


<pre>
$ ls -l /dev/sdb3
brw-rw----. 1 root disk 8, 19 May 21 09:14 /dev/sdb3

</pre>





<strong>2. Apply cryptsetup's luksFormat</strong>

Linux Unified Key Setup (LUKS): This is the name for Linux’s partition encryption system, and works by applying a special kind of encrypted file-system container. LUKS is ideal if you want to use it to encrypt contents of usb-pens.

Before we apply the luksFormat, let's first see if sdb3 currently has a file system installed on it:

<pre>
$ blkid | grep "^/dev/sdb3"
</pre>

As expected, it doesn't. 

Now let's install the luksFormat:

<pre>
$ cryptsetup luksFormat /dev/sdb3

WARNING!
========
This will overwrite data on /dev/sdb3 irrevocably.

Are you sure? (Type uppercase yes): YES
Enter passphrase:
Verify passphrase:

</pre>

Now let's check blkid again:

<pre>
$ blkid | grep "^/dev/sdb3"
/dev/sdb3: UUID="5838a034-cfc2-4301-8cbc-c8c26f63fd75" TYPE="crypto_LUKS"
</pre>

Now it does have it. This is a special filesystem that essentially acts as a secure container which is designed to hold an ordinary filesystem, such as ext4, which we'll install further down. 

<strong>3. Unlock the encrypted partition using cryptsetup's luksOpen</strong>
Before we unlock the encrypted, partitions, let's first see our list of block devices:


<pre>
$ lsblk
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   20G  0 disk
├─sda1            8:1    0  500M  0 part /boot
└─sda2            8:2    0 19.5G  0 part
  ├─centos-swap 253:0    0    2G  0 lvm  [SWAP]
  └─centos-root 253:1    0 17.5G  0 lvm  /
sdb               8:16   0  2.1G  0 disk
├─sdb1            8:17   0   10M  0 part
├─sdb2            8:18   0   10M  0 part
├─sdb3            8:19   0   10M  0 part
├─sdb4            8:20   0    1K  0 part
├─sdb5            8:21   0   10M  0 part
├─sdb6            8:22   0   10M  0 part
└─sdb7            8:23   0 1001M  0 part
sr0              11:0    1 55.4M  0 rom
</pre>


Let's now unlock our encrypted partition: 


<pre>
$ ls -l /dev/mapper/ | grep decryptedpartition

$ cryptsetup luksOpen /dev/sdb3 decryptedpartition
Enter passphrase for /dev/sdb3:
</pre>


Now let's view lsblk again:


<pre>
$ lsblk
NAME                   MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
sda                      8:0    0   20G  0 disk
├─sda1                   8:1    0  500M  0 part  /boot
└─sda2                   8:2    0 19.5G  0 part
  ├─centos-swap        253:0    0    2G  0 lvm   [SWAP]
  └─centos-root        253:1    0 17.5G  0 lvm   /
sdb                      8:16   0  2.1G  0 disk
├─sdb1                   8:17   0   10M  0 part
├─sdb2                   8:18   0   10M  0 part
├─sdb3                   8:19   0   10M  0 part
<strong>│ └─decryptedpartition 253:2    0    8M  0 crypt
</strong>├─sdb4                   8:20   0    1K  0 part
├─sdb5                   8:21   0   10M  0 part
├─sdb6                   8:22   0   10M  0 part
└─sdb7                   8:23   0 1001M  0 part
sr0                     11:0    1 55.4M  0 rom

</pre>



As a result, this has created the following block device:

<pre>
$ ls -l /dev/mapper/ | grep decryptedpartition
lrwxrwxrwx. 1 root root       7 May 21 09:26 decryptedpartition -> ../dm-2

$ ls -l /dev | grep dm-2
brw-rw----. 1 root disk    253,   2 May 21 09:26 dm-2
</pre>

We can now chech the status of our newly decrypted block device, either via the symbolic link:

<pre>
$ cryptsetup status /dev/mapper/decryptedpartition
/dev/mapper/decryptedpartition is active.
  type:    LUKS1
  cipher:  aes-xts-plain64
  keysize: 256 bits
  device:  /dev/sdb3
  offset:  4096 sectors
  size:    16384 sectors
  mode:    read/write

</pre>

or directly:

<pre>
$ cryptsetup status /dev/dm-2
/dev/dm-2 is active.
  type:    LUKS1
  cipher:  aes-xts-plain64
  keysize: 256 bits
  device:  /dev/sdb3
  offset:  4096 sectors
  size:    16384 sectors
  mode:    read/write
</pre>




<strong>4. Format the decrypted block device</strong>

Now let's install a file system onto the partition:

<pre>
$ mkfs.ext4 /dev/mapper/decryptedpartition
mke2fs 1.42.9 (28-Dec-2013)
Filesystem label=
OS type: Linux
Block size=1024 (log=0)
Fragment size=1024 (log=0)
Stride=0 blocks, Stripe width=0 blocks
2048 inodes, 8192 blocks
409 blocks (4.99%) reserved for the super user
First data block=1
Maximum filesystem blocks=8388608
1 block group
8192 blocks per group, 8192 fragments per group
2048 inodes per group

Allocating group tables: done
Writing inode tables: done
Creating journal (1024 blocks): done
Writing superblocks and filesystem accounting information: done

</pre>


<strong>5. Mount your decrypted partition</strong>

Let's first create the mountpoint:


<pre>$ mkdir /tmp/xfsmountpoint/</pre>


Just before the mounting, let's check the block device's status:

<pre>
cryptsetup status /dev/mapper/decryptedpartition
/dev/mapper/decryptedpartition is active.
  type:    LUKS1
  cipher:  aes-xts-plain64
  keysize: 256 bits
  device:  /dev/sdb3
  offset:  4096 sectors
  size:    16384 sectors
  mode:    read/write
</pre>




Then do the mounting:


<pre>
$ mount /dev/mapper/decryptedpartition /tmp/xfsmountpoint/
</pre>


Now let's check the status again:

<pre>
 cryptsetup status /dev/mapper/decryptedpartition
/dev/mapper/decryptedpartition is active <strong>and is in use</strong>.
  type:    LUKS1
  cipher:  aes-xts-plain64
  keysize: 256 bits
  device:  /dev/sdb3
  offset:  4096 sectors
  size:    16384 sectors
  mode:    read/write


</pre>





<h2>Closing an encrypted partition</h2>


After you have finished working on your encrypted partition, you can then close it by taking the following steps:

<ol>
	<li>unmount the encrypted file system, <code>umount /dev/mapper/decryptedpartition</code></li>
	<li>Then close your decrypted partition, <code>cryptsetup luksClose /dev/mapper/decryptedpartition</code>, note the uppercase "C", this results in the deletion of the /dev/mapper/decryptedpartition block device. </li>
</ol>