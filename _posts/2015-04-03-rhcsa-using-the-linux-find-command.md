---
ID: 339
post_title: 'RHCSA &#8211; Using the Linux find command'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-using-the-linux-find-command
published: true
post_date: 2015-04-03 00:00:00
---
The "find" command is the main command to use if you are trying to locate a particular file or folder. 

The find command is incredibly versatile and has lots of features. So the best way to understand how to use it is by taking a look at some examples:




<h2>Find a file by it's name</h2>

Let's say I want to find a file called "sshd_config" but have no idea which directory it might be in, in that case I do: 

<pre>
$ find / -name sshd_config -type f
/etc/ssh/sshd_config
</pre>


Let's break down whats happening here:

<ul>
	<li><code>/</code> - first argument that I specified is an absolute path, which in my case is the root directory, "/". This tells the find command where to search, which in my case is the entire linux filesystem. Hence this command may take several minutes to complete, as there's a lot of files/folders to search through. That's why you should specify a lower level folder in order to narrow the search scope and get a faster response. 

</li>
	<li><code>-name sshd_config</code> - The "name" option is used to tell the find command, to perform a search and return all results based on the file's (or folder's) name. In this case I specified that the name should be "sshd_config". You can also make use of wildcards here.</li>
	<li><code>-type f</code> - Here I specified that I am looking for files and not directories. This argument is optional and if I omit it, then the find command will return matches for both files and folders. </li>
</ul>


Note you can omit specifying a path, in which case the find command will default to searching through the cwd. Therefore the following:  

<pre>
$ find -name sshd_config -type f
</pre>

is the same as writing: 


<pre>
$ find . -name sshd_config  -type f
</pre>


<h2>Find a folder by it's name</h2>
For example, to locate all directories beginning with "ssh", and located somewhere in /etc, I do:

<pre>
$ find /etc -name ssh* -type d
</pre>

Note: you cannot be in your home directory when running the above command, because it won't work. I think this could be a bug. 

Here's I specified the type as "d", which mean's I want to see matches that are directories only.  

If I omit to specify a type, then the find command will return all matches, i.e. files and folders. 

<h2>Find a file that has been recently accessed</h2>
So far we matched files/folders based on the "-name" condition (aka test) being satisfied. However there are lots of other tests that are available, which you can view in the man page. E.g you can use "-amin -x" to list all files that have been accessed less than x minutes ago.  You can then combine these tests together like this:

<pre>
[root@localhost /]# find / -name ssh* -amin -50 -type d
/root/sshd_config

</pre>

<h2>Alternatives to the find command</h2>
The find command is really powerful. But there are other user-friendly (but less powerful) commands that you can use to locate files, they are the <a href="http://codingbee.net/tutorials/rhcsa/rhcsa-find-help/" title="RHCSA - Finding help on the commandline">which, locate, and whereis commands</a>.  



See also:

http://www.tecmint.com/35-practical-examples-of-linux-find-command/