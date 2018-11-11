---
ID: 344
post_title: 'RHCSA &#8211; Copying files from one server to another using SCP'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-copying-files-from-one-server-to-another-using-scp
published: true
post_date: 2015-04-06 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="Assume you are logged in as the user 'tom' on machine 'LinuxA'. Let’s say that there is a machine called 'LinuxB' and there is a user account called 'jerry' on machine 'LinuxB'. Also on LinuxB you have the file, '/tmp/testfile.txt'. Now what is the command to copy /tmp/testfile.txt from LinuxB to the tom’s home directory on LinuxA?"]
[tom@LinuxA ~]$ scp jerry@LinuxB:/tmp/testfile.txt /home/tom
[/toggle]
[toggle title="Following on from the above scenario, What is the command to copy /home/tom/testfile.txt to jerry's home directory on LinuxB?"]
[tom@LinuxA ~]$ scp /home/tom/testfile.txt jerry@LinuxB:/home/jerry/testfile.txt
[/toggle]
[/accordion]

<hr/>


What if you want to copy files from one machine to another machine. There are a few ways to do this, e.g. via ftp, nfs, cifs....etc. We will cover all these options later. But for now we'll cover the easiest file copying option, called scp, which comes as part of the ssh suite.  

The scp is like using the cp command, but this time you have to specify the remote machine's name, and username. 

For example, you are logged in as the user tom on machine LinuxA. Let's say that there is a machine called LinuxB and there is a user account called 'jerry' on machine LinuxB. Also on LinuxB you have the file, /tmp/testfile.txt. Now here's the command to copy /tmp/testfile.txt from LinuxB to the tom's home directory on LinuxA.:

<pre>
[tom@LinuxA ~]$ scp jerry@LinuxB:/tmp/testfile.txt /home/tom
jerry@LinuxB's password:
testfile.txt                                                                                      100%   12     0.0KB/s   00:00
$ ls -l
total 4
-rw-r--r--. 1 tom tom 12 Nov 14 13:06 testfile.txt

</pre>   

Note, here we got prompted to enter jerry's password. However if you have set up ssh private/public keys, as in the last article, then you won't get prompted to enter the password, since scp is essentially ssh with a bit of extra syntax. 

Now let's say you want to copy to file back to LinuxB but this time place it in the /home/jerry directory, then you do:


<pre>
[tom@LinuxA ~]$ scp /home/tom/testfile.txt jerry@LinuxB:/home/jerry/testfile.txt
jerry@puppetagent01's password:
testfile.txt                                                                                      100%   12     0.0KB/s   00:00
</pre>

Now let's check this has worked:

<pre>
[tom@LinuxA ~]$ ssh jerry@LinuxB
jerry@puppetagent01's password:
[jerry@puppetagent01 ~]$ ls -l /home/jerry/
total 4
-rw-r--r--. 1 jerry jerry 12 Nov 14 13:13 testfile.txt
</pre>


<h2>Further reading</h2>
If you are using a windows machine, and want to copy files to/from a linux machine, then you can still use scp, by firsting installing <a href="https://winscp.net/eng/download.php" rel="nofollow">winscp</a> on your windows machine.