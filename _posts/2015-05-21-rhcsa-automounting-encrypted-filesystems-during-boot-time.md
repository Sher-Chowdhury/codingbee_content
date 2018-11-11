---
ID: 396
post_title: 'RHCSA &#8211; Automounting Encrypted Filesystems during boot time'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-automounting-encrypted-filesystems-during-boot-time
published: true
post_date: 2015-05-21 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is the entry you need to add to the fstab file to automount the decrypted ext4 based '/dev/mapper/decryptedpartition' on to the mount point, '/tmp/secretmount'?"]
/dev/mapper/decryptedpartition   /tmp/ext4secretmount  ext4 defaults 1 2
[/toggle]
[toggle title="Which file do you need to add an entry in order to prompt your machine to request for the encrypted filesystem's password during boot up?"]
/etc/crypttab
[/toggle]
[toggle title="This file is usually empty, so what entry do you need to add this file to autoprompt you for the password on bootup?"]
decryptedpartition    /dev/sdb3      none
[/toggle]
[toggle title="However what entry do you need to add this file so that the password is automatically inputted for you on boot up?"]
decryptedpartition    /dev/sdb3      {password in plaintesxt}
[/toggle]

[/accordion]

<hr/>



In the last lesson we saw how to manually mount a luks encrypted partition. 


However as mentioned earlier, any partitions (including encrypted partitions) that are mounted directly using the mount command will become unmounted again when you reboot the machine. To overcome this we would need to add an entry to the fstab file. Following on from the last lesson's example, we have:


<pre>$ blkid | grep "^/dev/sdb3"
/dev/sdb3: UUID="5838a034-cfc2-4301-8cbc-c8c26f63fd75" TYPE="crypto_LUKS"
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

Then we open up access to our decrypted block device by running:

<pre>
$ cryptsetup luksOpen /dev/sdb3 decryptedpartition
Enter passphrase for /dev/sdb3:
Enter passphrase for /dev/sdb3:
</pre>

After that we have now exposed the decrypted block device that we want to mount:

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
│ └─decryptedpartition 253:2    0    8M  0 crypt
├─sdb4                   8:20   0    1K  0 part
├─sdb5                   8:21   0   10M  0 part
├─sdb6                   8:22   0   10M  0 part
└─sdb7                   8:23   0 1001M  0 part
sr0                     11:0    1 55.4M  0 rom
</pre>

Now to automount this, we would need to add the following line in our fstab:


<pre>
$ cat /etc/fstab

#
# /etc/fstab
# Created by anaconda on Sat Mar 14 19:14:11 2015
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/centos-root /                       xfs     defaults        1 1
UUID=547c78ce-34b5-4e31-84d2-bf72b196fa57 /boot                   xfs     defaults        1 2
/dev/mapper/centos-swap swap                    swap    defaults        0 0
<strong>/dev/mapper/decryptedpartition   /tmp/ext4secretmount  ext4 defaults 1 2
</strong>

</pre>

Now to confirm that this works, we do a "mount all":


<pre>
$ mount -a
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
<strong>│ └─decryptedpartition 253:2    0    8M  0 crypt /tmp/ext4secretmount</strong>
├─sdb4                   8:20   0    1K  0 part
├─sdb5                   8:21   0   10M  0 part
├─sdb6                   8:22   0   10M  0 part
└─sdb7                   8:23   0 1001M  0 part
sr0                     11:0    1 55.4M  0 rom
</pre>
 
Success!


However there is a problem, we generated the /dev/mapper/decryptedpartition block device by running the "cryptsetup luksOpen" command, however this isn't persistant. So to make this block device available again, you need to run "cryptsetup luksOpen" after every boot. Since the fstab file is referring a block device that doesn't exist yet, automounting will fail. 

In this situation, the solution is to prompt the machine (during boot time) to first unlock encrypted partitions before it reads the /etc/fstab and starts the automounting process. This is achieved by adding an entry to the /etc/crypttab file:


<pre>
$ cat /etc/crypttab
decryptedpartition    /dev/sdb3      none
</pre> 


The first 2 fields are essentially the same parameters that are passed into the <code>cryptsetup luksOpen...</code> command. 

The last field shows "none", which means that we don't want to use an automated approach to passing in a password. 
As a result the machine will now prompt for a password at the very early stages of booting up.