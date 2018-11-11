---
ID: 340
post_title: 'RHCSA &#8211; Compressing files and folders using gzip and tar'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-compressing-files-and-folders-using-gzip-and-tar
published: true
post_date: 2015-04-03 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What are the 3 main modes of tar?"]
- c: creating tar files
- x: extracting tar files
- t: displaying table of contents of a tar file
[/toggle]
[toggle title="What is the command to extract 'testfile.tar.gz'?"]
$ tar -xzvf testfile.tar.gz   
[/toggle]
[toggle title="What is the command to create a compressed archive for the folder '/tmp/testfolder' into a file called 'name.tar.gz'?"]
$ tar -cvzf name.tar.gz /tmp/testfolder     
[/toggle]
[toggle title="What is the command to compress the file 'testfolder.tar'?"]
$ gzip testfolder.tar    # this will rename this file to testfolder.tar.gz
[/toggle]
[toggle title="What is the command to uncompress the file '/tmp/testfolder.tar.gz'?"]
$ gunzip /tmp/testfolder.tar.gz
[/toggle]
[toggle title="What is the command to compress /tmp/testfile1.txt using bzip?"]
$ bzip2 /tmp/testfile1.txt    # this will rename file to testfile1.txt.bz2
[/toggle]
[toggle title="What is the command to uncompress /tmp/testfile1.txt.bz2 using bzip?"]
$ bunzip2 /tmp/testfile1.txt.bz2
[/toggle]
[toggle title="What is the command to list the contents of /tmp/testfolder.tar.gz?"]
$ tar -tf /tmp/testfolder.tar.gz
[/toggle]
[toggle title="What is the command to create the the gzipped tar file 'testfolder.tar.gz' from the folder, 'testfolder', using the star utility?"]
$ star -czv -f=testfolder.tar.gz testfolder
[/toggle]
[toggle title="What is the command to install the star utility?"]
$ yum install star
[/toggle]
[toggle title="What is the command to extract the file testfolder.tar.gz, using the star utility?"]
$ star -xzv -f=testfolder.tar.gz
[/toggle]
[toggle title="What is the star command to view the content of the file testfolder.tar.gz?"]
$ star -tv -f=testfolder.tar.gz
[/toggle]
[/accordion]

<hr/>


<h2>Compressing files</h2>
There are 2 main commands that you can use for compressing files <code>gzip</code> and <code>bzip2</code>. 





<h3>Using the gzip file compression utility</h3>
gzip is the more popular command out of the 2. gzip is actually really easy to use, here's an example. First let's create a file that we want to compress:


<pre>
$ echo "hello world" > testfile1.txt
$ file testfile1.txt
testfile1.txt: ASCII text
</pre>

Here we use the <code>file</code> command to confirm the type of file this is. We used the ".txt" for our benefit, and it as an indicator to us of what type of file this is. So it is best practice to always add a meaningful suffix to the files you create. E.g. if you create a shell script, you should give it a suffix such ".sh". However filename suffixes like ".txt" and ".sh" doesn't have any special meaning to Linux. Instead Linux determines a file's type by analyzing its content, in the same way that the <code>file</code> command does. Since a suffix is something that we assign to files ourselves, it means that a filename's can be wrong/inaccurate due to human error. Therefore if a file doesn't have a suffix, or we suspect that it could be wrong, then we can check a file's type using the <code>file</code> command.    


Now here's how to compress this file using gzip:


<pre>
$ gzip testfile1.txt
$ ls -l
total 4
-rw-r--r--. 1 root root 46 Oct 23 19:00 testfile1.txt.gz
$ file testfile1.txt.gz
testfile1.txt.gz: gzip compressed data, was "testfile1.txt", from Unix, last modified: Fri Oct 23 19:00:14 2015
</pre>

As you can see, the gzip command not only compressed the file, but also added it's own suffix to the resulting compressed file, ".gz". This again is for our benefit only and doesn't have any special meaning to Linux or to the gzip utility.  

Now to unzip, we can use the gzip's command's (d)ecompress option. Or we use the gunzip command:

<pre>
$ gunzip testfile1.txt.gz
$ ls -l
total 4
-rw-r--r--. 1 root root 12 Oct 23 19:00 testfile1.txt
$ file testfile1.txt
testfile1.txt: ASCII text
</pre>




<h3>Using the bzip2 file compression utility</h3>

Now here's how we compress a file using bzip2:

<pre>
$ bzip2 testfile1.txt
$ ls -l
total 4
-rw-r--r--. 1 root root 52 Oct 23 19:00 testfile1.txt.bz2
$ file testfile1.txt.bz2
testfile1.txt.bz2: bzip2 compressed data, block size = 900k
</pre>

Once again bzip2 has added the bz2 suffix for our benefit. Now to uncompress, we can use the bzip command's -d option, or we can use the <code>bunzip2</code> command:


<pre>
$ bunzip2 testfile1.txt.bz2
$ ls -l
total 4
-rw-r--r--. 1 root root 12 Oct 23 19:00 testfile1.txt
$ file testfile1.txt
testfile1.txt: ASCII text
</pre>



<h2>Compressing whole directories using tar</h2>

gzip and bzip2 on their own can't compress a directory. They can only compress files. However the tar command can convert (i.e. archive) a file into a single file, which is referred to as a "tar" file. For example let's say we have the following folder, called "testfolder":

<pre> 
$ pwd
/home/codingbee/testfolder
$ ls -l 
total 12
drwxr-xr-x. 5 root root    91 Oct 23 19:58 testfolder
$ file testfolder
testfolder: directory
$ tree
.
└── testfolder
    ├── file2
    ├── file3
    ├── folder1
    │     ├── testfile2.txt
    │     └── testfile3.txt
    ├── folder2
    ├── folder3
    │     └── testfile4.txt
    └── testfile.txt

4 directories, 6 files
</pre>

Now here's how we create a tar file from this folder:

<pre>
$ tar -cvf testfolder.tar testfolder
testfolder/
testfolder/file2
testfolder/file3
testfolder/folder1/
testfolder/folder1/testfile2.txt
testfolder/folder1/testfile3.txt
testfolder/folder2/
testfolder/folder3/
testfolder/folder3/testfile4.txt
testfolder/testfile.txt
$ ls -l
total 12
drwxr-xr-x. 5 root root    91 Oct 23 19:58 testfolder
-rw-r--r--. 1 root root 10240 Oct 23 20:03 testfolder.tar
$ file testfolder.tar
testfolder.tar: POSIX tar archive (GNU)
</pre>

You should read this tar command as 




<blockquote>
Create verbosely, a tar archive with filename of 'testfolder.tar' using contents from 'testfolder'.
</blockquote>



Note, don't use the full path to the testfolder. Instead cd into the directory and then run the tar command. 

Note that tar keeps the original directory intact, and just generates a tar equivalent for it. Also note that the tar command doesn't automatically add any suffixes to the resulting tar file, e.g. a .tar suffix. That's why we manually added this as shown above. 

However if you want to also compress a directory and not just create a tar file, then you can run the gzip command on the tar file:


<pre>
$ gzip testfolder.tar
$ ls -l
total 4
drwxr-xr-x. 5 root root  91 Oct 23 19:58 testfolder
-rw-r--r--. 1 root root 442 Oct 23 20:09 testfolder.tar.gz
$ file testfolder.tar.gz
testfolder.tar.gz: gzip compressed data, was "testfolder.tar", from Unix, last modified: Fri Oct 23 20:09:17 2015
</pre>

So to compress a folder, we had to first had to run the tar command followed by the gzip command. However a faster way to achieve the same result is to instruct the tar command to automatically gzip the tar file straight after it has been created. This is done using the tar command's -z option:


<pre>
$ ls -l
total 0
drwxr-xr-x. 5 root root 91 Oct 23 19:58 testfolder
$ tar -cvzf testfolder.tar.gz testfolder
tar: Removing leading `/' from member names
testfolder/
testfolder/file2
testfolder/file3
testfolder/folder1/
testfolder/folder1/testfile2.txt
testfolder/folder1/testfile3.txt
testfolder/folder2/
testfolder/folder3/
testfolder/folder3/testfile4.txt
testfolder/testfile.txt
$ ls -l
total 4
drwxr-xr-x. 5 root root  91 Oct 23 19:58 testfolder
-rw-r--r--. 1 root root 437 Oct 23 20:27 testfolder.tar.gz
$ file testfolder.tar.gz
testfolder.tar.gz: gzip compressed data, from Unix, last modified: Fri Oct 23 20:27:09 2015
</pre>

Note, if you want tar to use the bzip2 instead of gzip, then you simply using the tar command's "j" option instead of the "z" option.  


<h2>Uncompressing tar files back into directories</h2>




<pre>
$ ls -l
total 4
-rw-r--r--. 1 root root 427 Oct 23 20:51 testfolder.tar.gz
$ tar -xvzf testfolder.tar.gz
testfolder/
testfolder/file2
testfolder/file3
testfolder/folder1/
testfolder/folder1/testfile2.txt
testfolder/folder1/testfile3.txt
testfolder/folder2/
testfolder/folder3/
testfolder/folder3/testfile4.txt
testfolder/testfile.txt
</pre>

You should read this command as:



<blockquote>
Extract verbosely the gzipped tar archive of the filename testfolder.tar.gz
</blockquote>




Now let's check this has worked:

<pre>
$ ls -l
total 4
drwxr-xr-x. 5 root root  91 Oct 23 19:58 testfolder
-rw-r--r--. 1 root root 427 Oct 23 20:51 testfolder.tar.gz
$ tree testfolder
testfolder
├── file2
├── file3
├── folder1
│     ├── testfile2.txt
│     └── testfile3.txt
├── folder2
├── folder3
│     └── testfile4.txt
└── testfile.txt

3 directories, 6 files

</pre>

One thing to remember is that doing an extract like this will overwrite any files/folders that it will get extracted to, here's an example:


<pre>
$ cat testfolder/testfile.txt
hello wooooorld
$ tar -xvf testfolder.tar
testfolder/
testfolder/file2
testfolder/file3
testfolder/folder1/
testfolder/folder1/testfile2.txt
testfolder/folder1/testfile3.txt
testfolder/folder2/
testfolder/folder3/
testfolder/folder3/testfile4.txt
testfolder/testfile.txt
$ cat testfolder/testfile.txt
apple pie
apple cake
banofee cake
orange soda
brown bread
cottage pie
chocolate cake
white bread
upside down pineapple cake
bread and butter pudding
$
</pre>
 
One way to avoid accidantically overwrite 

<h2>Looking inside a tar file</h2>

You can look inside a tar file using the -t option:


<pre>
$ tar -tf testfolder.tar.gz
testfolder/
testfolder/file2
testfolder/file3
testfolder/folder1/
testfolder/folder1/testfile2.txt
testfolder/folder1/testfile3.txt
testfolder/folder2/
testfolder/folder3/
testfolder/folder3/testfile4.txt
testfolder/testfile.txt
</pre>




<h2>Compressing using the star utility</h2>

Standard Tape ARchiver, aka star, is actually a wrapper around tar, which offers some other features on top of tar, for example it avoids overwriting existing files/folders during extracting., but it isn't installed by default, so to use this utility you first have to install it:

<pre>
$ yum install star   
</pre>


Afte that, to create an archive you do:


<pre>
$ ls -l
total 0
drwxr-xr-x. 5 root root 91 Oct 23 19:58 testfolder
$ star -cz -f=testfolder.tar.gz testfolder/
star: 1 blocks + 0 bytes (total of 10240 bytes = 10.00k).
$ ls -l
total 4
drwxr-xr-x. 5 root root  91 Oct 23 19:58 testfolder
-rw-r--r--. 1 root root 458 Oct 23 21:26 testfolder.tar.gz
$ file testfolder.tar.gz
testfolder.tar.gz: gzip compressed data, from Unix, last modified: Fri Oct 23 21:26:32 2015
$
</pre>

Note: we stick with the same ".tar.gz" extension convention to keep this simple. star also doesn't have 


To extract, you do:

<pre>
$ star -x -z -f=testfolder.tar.gz
star: current 'testfolder/' newer.
star: current 'testfolder/file2' newer.
star: current 'testfolder/file3' newer.
star: current 'testfolder/folder1/' newer.
star: current 'testfolder/folder1/testfile2.txt' newer.
star: current 'testfolder/folder1/testfile3.txt' newer.
star: current 'testfolder/folder2/' newer.
star: current 'testfolder/folder3/' newer.
star: current 'testfolder/folder3/testfile4.txt' newer.
star: current 'testfolder/testfile.txt' newer.
star: 1 blocks + 0 bytes (total of 10240 bytes = 10.00k).

</pre>

Note, this process will ONLY overwrite if archive files are newer than those that are on the Operating System. If the OS has newer versions of the corresponding archive files, then star will skip extracting the older archived files. 

Also you can uncompress star files using tar as well. 


You can also use star to extract just a single file:


<pre>
$ tree
.
├── testfolder
│     ├── file2
│     ├── file3
│     ├── folder1
│     │     ├── testfile2.txt
│     │     └── testfile3.txt
│     ├── folder2
│     ├── folder3
│     │     └── testfile4.txt
│     └── testfile.txt              # lets first delete this file
└── testfolder.tar.gz

4 directories, 7 files
$ rm testfolder/testfile.txt
rm: remove regular file ‘testfolder/testfile.txt’? y
$ tree
.
├── testfolder
│     ├── file2
│     ├── file3
│     ├── folder1
│     │     ├── testfile2.txt
│     │     └── testfile3.txt
│     ├── folder2
│     └── folder3
│         └── testfile4.txt
└── testfolder.tar.gz

4 directories, 6 files
$ star -t -f=testfolder.tar.gz
star: WARNING: Archive is 'gzip' compressed, trying to use the -z option.
testfolder/
testfolder/folder1/
testfolder/folder1/testfile2.txt
testfolder/folder1/testfile3.txt
testfolder/folder2/
testfolder/folder3/
testfolder/folder3/testfile4.txt
testfolder/file2
testfolder/file3
testfolder/testfile.txt         # we want to just extract this file
star: 1 blocks + 0 bytes (total of 10240 bytes = 10.00k).
$ star -x -z -f=testfolder.tar.gz testfolder/testfile.txt
star: 1 blocks + 0 bytes (total of 10240 bytes = 10.00k).
$ tree
.
├── testfolder
│     ├── file2
│     ├── file3
│     ├── folder1
│     │     ├── testfile2.txt
│     │     └── testfile3.txt
│     ├── folder2
│     ├── folder3
│     │     └── testfile4.txt
│     └── testfile.txt
└── testfolder.tar.gz

4 directories, 7 files
$

</pre>