---
ID: 347
post_title: >
  Understanding Hard and Soft Links on
  CentOS/RHEL 7
author: sher
post_excerpt: |
  In windows you'll have come across shortcuts, e.g. desktop shortcuts. In windows, shortcuts gives you access to the same thing but from a different location. In Linux you can do the same thing, but with a feature called "links".
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/understanding-hard-and-soft-links-on-centos-rhel-7
published: true
post_date: 2018-03-27 00:00:00
---
In windows you'll have come across shortcuts, e.g. desktop shortcuts. In windows, shortcuts gives you access to the same thing but from a different location. In Linux you can do the same thing, but with a feature called "links". Links allow users to edit the same file from different locations.  There are 2 types of links:

<ol>
	<li>soft links (aka symbolic links)</li>
	<li>hard links</li>
</ol>

Both of these types are links are created using the <code>ln</code> command. To understand how symbolic and hard links differ from one another, you need to first understand inodes:


<h2>Hard links</h2>

An inode (aka “index node”) is an entry in a filesystem table that reference’s a location in a filesystem. In other words, it's like a reference book has an index at the back, containing a list of keys along with page numbers, a filesystem also has an index, but instead of the keys being keyword-terms, they are the absolute paths to files, and instead of page numbers, it lists inode values.

You can look-up the inode value for a particular file using the ls command's -i option:

<pre>
$ echo "hello world" > testfile.txt
$ ls -li /tmp/testfile.txt
<strong>102113012</strong> -rw-r--r--. <strong>1</strong> root root 12 Apr  3 19:08 /tmp/testfile.txt
</pre>  
In this example, the key is "/tmp/testfile.txt" and it's inode value is "102113012"

Also notice the third column, which shows a "1". This says how many entries in the index points to the same inode value. inode works a bit like the index of a reference book, where a file's name are entries in the index, and inodes are the corresponding page numbers, which points to a page (bit of data) in the book. This analogy is how hard-links works, if you imagine hard links as the index's entry, and inode values are the page numbers. Hence creating a hard link is like creating a new entry in the index, but references the same inode value (aka page number). 



Here’s how to create a hard-link:

<pre>
$ ln /tmp/testfile.txt /tmp/anothertestfile.txt
</pre>

Now let's confirm that both the original filename and the hardlink have the same inode:

<pre>
$ ls -li /tmp
total 8
102113012 -rw-r--r--. <strong>2</strong> root root 12 Apr  3 19:20 anothertestfile.txt
102113012 -rw-r--r--. <strong>2</strong> root root 12 Apr  3 19:20 testfile.txt
</pre>

As you can see, the earlier "1" value has now incremented to "2". Hard links have the following characteristics:


<ul>
	<li>All newly created directories always start with a inode value of “2”, that’s because of the “.” directory that’s automatically created within the folder, is actually an auto-generated hardlink.</li>
	<li>it is not possible to create a hard link for a folder....you will get an error message if you try.</li>
	<li>hard links have and actual paths always have the same ugo+rwo permissions, if you change the permissions using chmod for the hard-link it will also automatically get reflected on the main link too. </li>
	<li>you can only create hard-links for files that you at least have read permissions for. This is for security reasons. Obviously if you are logged in as the root user, then you won't experience this restriction.</li>
	<li>Hard links don't work across filesystems - If you want to create a hard link then the hard link must reside in the same filesystem where the original file is. 
<pre>
[root@localhost ~]# lsblk
NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
sda               8:0    0  64G  0 disk
├─sda1            8:1    0   1G  0 part /boot
└─sda2            8:2    0  63G  0 part
  ├─centos-root 253:0    0  41G  0 lvm  /
  ├─centos-swap 253:1    0   2G  0 lvm  [SWAP]
  └─centos-home 253:2    0  20G  0 lvm  /home
[root@mariadb-client ~]# echo 'hello' > /root/testfile.txt
[root@mariadb-client ~]# ln /root/testfile.txt /boot/testfilecopy.txt
<strong>ln: failed to create hard link ‘/boot/testfilecopy.txt’ => ‘/root/testfile.txt’: Invalid cross-device link</strong>
</pre>
If you need to create links across filesystems then it's possible to do this by creating soft links which we'll cover further down. Similarly, hard-link stays intact if you move the file, or the hard-link to a different folder as long as this folder is within the same filesystem. This again is analogous to changing the book index's key (absolute path to file), but page number (inode value) stays the same. This analogy breaks down if you move the file to a different filesystem, as this is analogous to tearing out the page from one book and placing it in another book.
</li>
	<li>the hard link stays intact even if you change the file’s name, or the hard link's name, or both. This is like amending one of the book index's key (i.e. absolute filepath and filename) but the page number (i.e. inode value) is left unchanged left unchanged.</li>
	<li>deleting a hardlink is like deleting an entry from a book's index. The page (i.e. file) isn't deleted. If all book index entries (hard links) are deleted for a given page (i.e. file) then that page can be thought of as innacessible (i.e. deleted)</li>


</ul>

 
Basically both the file-name and hard-link have equal claim to the actual bit of data, and in that respect, they are basically the same thing....pointers to an inode. Therefore if you do move a file to another device block, then which ever pointer, you used to perform this task will remain intact (i.e. both the index key and value will be updated accordingly), whereas the other will no longer reference this file. 

However the link breaks when you move the file-or-link to a new block device (i.e. new filesytem). It’s basically like moving the page (bit of data) to a different book. In this scenario, as a failsafe, a copy of that file (with a new inode value) is generated on the new filesystem, so that the link/filename is still useable. just take caution, when making changes to one file, since it won’t be reflected in the other file.





<h2>soft links (aka symbolic links)</h2>

compared to hard links, soft links works more like how Microsoft Windows shortcuts.Soft links essentially redirects you to the source file. That means that soft links essentially gets broken if try to move the source-file to a different location, or rename this file. You create symbolic links by using the ln command again along with the (s)oft link option enabled:

<pre>
ln -s /tmp/testfile.txt /tmp/yetanothertestfile.txt
</pre>



Now let's check that this has worked by by using the ls command again:

<pre>

[root@localhost tmp]# ls -li
total 8
102113012 -rw-r--r--. 2 root root 12 Apr  3 19:20 anothertestfile.txt
102113012 -rw-r--r--. 2 root root 12 Apr  3 19:20 testfile.txt
102113022 <strong>l</strong><strong>rwxrwxrwx</strong>. 1 root root 17 Apr  3 19:34 <strong>yetanothertestfile.txt -> /tmp/testfile.txt</strong>
[root@localhost tmp]#


</pre>

Notice that symbolic links are denoted by "l" prefixed to the user-group-other permissions. Also notice that the inode value is different to that of the original file. Also notice that the filename has arrow pointing to the actual file. Also notice that by default symbolic links have 777 permissions. That's because the actual permissions are managed by the actual file that the soft-link is pointing to. 

Going back to our book analogy, when you lookup a certain term, you might find that instead of it giving a page number, it prompts you too "see" another keyword term. This is effectively what symbolic links are. However the symbolic link also has a inode value, that's because in Linux, everything can be thought of as a file, that included folders and symbolic links as well, since they all use up disk space (or pages) to store them.     



Also this doesn’t cause the file’s link value (ls -l, 2nd column) increment by one, since soft-links are conceptually different things to hard-links, as indicated by the fact that softlinks have different inode values compared to the source-file’s inode value.

Since soft-links reference the key of another file, it means that the soft-link breaks if you rename or move the source file.

&nbsp;

However compared to hardlinks, softlinks have 2 advantages:

<ul>
	<li>soft links works across filesystems (hdd, partitions,...etc)</li>
	<li>soft links can be used to link to directories.</li>
</ul>


<strong>See also:</strong>

&nbsp;

<a href="http://www.thegeekstuff.com/2012/01/linux-inodes/">http://www.thegeekstuff.com/2012/01/linux-inodes/</a> - learn about inodes

&nbsp;

<a href="http://www.thegeekstuff.com/2009/07/unix-stat-command-how-to-identify-file-attributes/">http://www.thegeekstuff.com/2009/07/unix-stat-command-how-to-identify-file-attributes/</a> - learn about the “stat” command, which gives various info about a file.

&nbsp;

<a href="http://www.thegeekstuff.com/2010/10/linux-ln-command-examples/">http://www.thegeekstuff.com/2010/10/linux-ln-command-examples/</a> - 9 and 10 are quite good to know. 9 basically says softlinks lets you virtually increases a diskspace                                                                                                                                                                                                    and 10 says that with hardlinks, a file isn’t deleted when you delete the source, you have to delete

all the hardlinks as well.

&nbsp;

<a href="http://www.cyberciti.biz/tips/understanding-unixlinux-symbolic-soft-and-hard-links.html">http://www.cyberciti.biz/tips/understanding-unixlinux-symbolic-soft-and-hard-links.html</a>

&nbsp;

[post-content post_name=rhsca-quiz]


[accordion]
[toggle title="What is the command to view the inode value for the file /tmp/testfile.txt?"]
$ ls -li /tmp/testfile.txt
[/toggle]
[toggle title="What is the command to create a hard link for /tmp/testfile.txt called /tmp/anothertestfile.txt?"]
$ ln /tmp/testfile.txt /tmp/anothertestfile.txt
[/toggle]
[toggle title="What command can you run to confirm this has worked?"]
$ ls -li /tmp/testfile.txt     # third column should show "2"
[/toggle]
[toggle title="True or False, hard links stay intact even if you change the file’s name, or the hard link's name, or both of them?"]
True
[/toggle]
[toggle title="True or False, hard-links still stay intact if you move the file, or the hard-link to another folder as long as this new target folder is within the same files system?"]
True
[/toggle]
[toggle title="True or false, you can create hard-links for folders?"]
false
[/toggle]
[toggle title="What is the command to create a soft/symbolic link for /tmp/testfile.txt called /tmp/yetanothertestfile.txt??"]
$ ln -s /tmp/testfile.txt /tmp/yetanothertestfile.txt
[/toggle]
[toggle title="True or False, soft-links still stay intact if you move the file, to another folder?"]
False
[/toggle]
[toggle title="True or False, hard-links will stay intact if you move the file to another folder which happens to reside in a different files system?"]
False
[/toggle]
[/toggle]
[toggle title="What advantages does soft links have to hard links?"]
Soft links can be used across multiple filesystems, and can create soft links for folders. 
[/toggle]
[/accordion]