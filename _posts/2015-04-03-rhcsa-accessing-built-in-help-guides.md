---
ID: 338
post_title: 'RHCSA &#8211; Accessing built-in help guides'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-accessing-built-in-help-guides
published: true
post_date: 2015-04-03 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="How do you view the 'tar' command's usage info?"]
It is usually one of the following:
$ tar h
# or
$ tar -h
# or 
$ tar help
# or
$ tar --help
[/toggle]
[toggle title="How do you view the 'tar' command's reference manual entry?"]
$ man tar
[/toggle]
[toggle title="What other commands can you run instead of the above??"]
$ info tar
# or 
$ pinfo tar
[/toggle]
[toggle title="What's the command to locate the name of the man entry for the config file, <code>/etc/selinux/config</code>?"]
$ whatis config
[/toggle]
[toggle title="What's the command to see if there is an available man page for the <code>/etc/passwd</code> config file?"]
$ whatis passwd
# you should discover the man page is accessible like this:
$ man 5 passwd
[/toggle]
[toggle title="What is the command to run before performing a search on the man pages?"]
$ mandb
[/toggle]
[toggle title="What's the command to search through all the man title+brief descriptions for the word 'compress'?"]
$ man -k compress
[/toggle]
[toggle title="What's the command to search through the entire man pages for the word 'compress'?"]
$ man -K compress    # note this time it is a capital k. 
[/toggle]
[toggle title="What's the command to open up the GUI based help guide?"]
$ yelp
[/toggle]
[toggle title="What is the command to view help info in firefox?"]
$ firefox file:///usr/share/doc
[/toggle]
[toggle title="What are the various commands for locating files?"]
Any of the following:
$ locate {command}
$ whereis {command}
$ which {command}
$ type {command}
[/toggle]
[toggle title="What command should you run first before using the 'locate' command?"]
$ updatedb
[/toggle]
[/accordion]

<hr/>




While most people rely on google when they get stuck, there are actually a number of useful help resources available from the command line.

You can also access help info from both the desktop gui (via the desktop user guide), but we'll just focus on finding help at the command line.


<h2>Viewing usage info</h2>

The man pages are quite long a detailed, which can be time consuming to read through, if you want a quick guide on how to use a command. That's why a lot of commands have a shorter (usage) guide that gives a much briefer overview of all a given command's options. The usage guide is accessible in one of several ways depending on the command. 

For example, to access the "ls" command's help info we use the "--help" switch

<pre>
$ ls --help | head
Usage: ls [OPTION]... [FILE]...
List information about the FILEs (the current directory by default).
Sort entries alphabetically if none of -cftuvSUX nor --sort is specified.

Mandatory arguments to long options are mandatory for short options too.
  -a, --all                  do not ignore entries starting with .
  -A, --almost-all           do not list implied . and ..
      --author               with -l, print the author of each file
  -b, --escape               print C-style escapes for nongraphic characters
      --block-size=SIZE      scale sizes by SIZE before printing them; e.g.,

</pre>

If <code>--help</code> doesn't give the usage info, then try <code>-h</code>, <code>help</code>, <code>-help</code>, or just <code>h</code>.

Some commands don't provide usage info, in which case you have to view their man pages instead. 


<h2>The man command</h2>

The RHEL OS comes with a Reference Manual. You can access various pages and sections of this manual using the 
"man" command is short for "Reference (man)ual". 

The man command displays pages from the official reference manual for a command. For example:

<pre>
$ man ls 
</pre>

You can even use the man command to view the help for in the man command itself:

<pre>
$ man man 
</pre>

It can be a bit overwhelming to take in the content from man pages, but it gets easier once you take the time to understand man pages in greater detail.

<h2>The info command</h2>

The info command is just another source of help information:

<pre>
$ info ls
</pre>

In most cases the info command gives the same content as the man command. However occasionally it does contains additional info. So it is probably best to only use 'info' after going through the man pages.

While in info mode, press the "h" key to list the various navigation options. 

This command is particularly useful for really long man pages, e.g.:


<pre>
$ info bash
</pre>

<h2>The pinfo command</h2>

The pinfo command display the man pages in a more user friendly format. It essentially breaks down a man pages into sections which you can navigate to using your arrow keys. Whereas the man command displays everything in a single output. Whether you use pinfo or man is down to personal preference.

<pre>
$ info bash
</pre>




<h2>The whatis command</h2>

The whatis command gives a one line description of a command:

<pre>
whatis ls
</pre>

whatis essentially works by looking up a given command's man page, and displays the header line from that man page. The whatis command is useful if you just want a quick reminder of a command without bringing up the whole man page.



However the whatis command is more useful for finding help info for various config file. For example if you want to find more info about the file <code>/etc/selinux/config</code> then do::



<pre>
$ whatis config
Config (3pm)         - access Perl configuration information
config (5ssl)        - OpenSSL CONF library configuration files
selinux_config (5)   - The SELinux sub-system configuration file.
</pre>

Once you have located a man page, you can then view it using the man command, by specifying it's location:


<pre>
$ man 5 selinux_config
</pre>



<h2>Searching the man pages</h2>

Before you search through the man pages, you should first run:

<pre>
$ mandb
</pre>

This will create/update an internal db using info from all the man pages. 


Sometimes you want to perform a task but dont know the name of the command to use. In those situations you will want to do a keyword search against the entire reference manual and then outputs a list of all man pages where a match has been found. for example, lets say you want to compress a file, but don't know what command you should use, in that case can do a keyword search on the word "compress", is the man command's k option:

<pre>
$ man -k compress
bunzip2 (1)          - a block-sorting file compressor, v1.0.6
bzcat (1)            - decompresses files to stdout
bzcmp (1)            - compare bzip2 compressed files
bzdiff (1)           - compare bzip2 compressed files
bzgrep (1)           - search possibly bzip2 compressed files for a regular expression
bzip2 (1)            - a block-sorting file compressor, v1.0.6
bzless (1)           - file perusal filter for crt viewing of bzip2 compressed text
.
.
.
. ...etc
</pre>

Note if you want to do a deeper search then use capital K instead:

<pre>
$ man -k compress
</pre>




As you can see it search's all "whatis" entries for search matches. 


Some software packages don't come with man pages. Instead, the man pages have to be installed separately (not sure how to identify these packages, might be possible using yum or rpm)


<h2>Accessing help from the desktop GUI</h2>

While it is possible to access a vast collection help resources straight from the command line, it can also be worthwhile accessing help from the desktop. This is especially true for help material that are in the form of images and diagrams.

There are a number of ways to access help:

The first way is:


<a href="http://codingbee.net/wp-content/uploads/2015/06/B1dCHP5.png"><img src="http://codingbee.net/wp-content/uploads/2015/06/B1dCHP5.png" alt="" width="653" height="395" class="alignnone size-full wp-image-4815" /></a>

Which takes you to:

<a href="http://codingbee.net/wp-content/uploads/2015/06/bNLMBxR.png"><img src="http://codingbee.net/wp-content/uploads/2015/06/bNLMBxR.png" alt="" width="824" height="537" class="alignnone size-full wp-image-4816" /></a>


You can also launch this utility by simply running the yelp command:

<pre>$ yelp</pre>


<h2>Viewing help info in usr/share/doc</h2>

Another place that contains help material <code>/usr/share/doc</code>, which is best viewed via firefox. You can launch firefox via the command line.

<pre>
$ firefox file:///usr/share/doc
</pre>

Which results in:

<a href="http://codingbee.net/wp-content/uploads/2015/06/A8xYMVV.png"><img src="http://codingbee.net/wp-content/uploads/2015/06/A8xYMVV.png" alt="" width="947" height="595" class="alignnone size-full wp-image-4817" /></a>



<h2>Tracking down a command</h2>


<pre>
type {command}      # tells you if command is builtin or not, if not then shows the path.
which {command}     # shows command's file location (even for builtin ones).
                    # this command is specifically suited for finding binaries.  
whereis {command}   # shows command's file location (even for builtin ones).
locate {command}    # shows command's file location 
</pre>
Note: you need to run the "updatedb" to run the "locate" command. This is the equivelent to the "mandb" is to the man command's -k option. 



<h3>The "type" and "which" commands</h3>
Before we start, let's first lookup the whatis info for the "type" and "which" commands:


<pre>
$ whatis type
type (1)             - bash built-in commands, see bash(1)
type (1p)            - write a description of command type
$ whatis which
which (1)            - shows the full path of (shell) commands.
</pre>

The "type" command tells you whether a command is builtin, or not, if not then it shows the path. E.g. 

<pre>
$ type cd
cd is a shell builtin
</pre>

You can find a list of all builtin commands listed in the type command's man page:

<pre>
$ man 1 type
</pre>


However even built-in files are represented in linux as a file, hence in this situation you need to use the "which" command to locate the binary (aka executable) file for the "cd" command:

<pre>
$ which cd
/usr/bin/cd
</pre>
 

Both "type" and "which" can be used to locate non-builtin commands:

<pre>
$ type tar
tar is /usr/bin/tar
$ which tar
/usr/bin/tar
</pre>

<h2>Locating documentation with rpm</h2>

You can also view what documentations have been installed using the rpm command. 

<pre>
$ rpm -qd tree
/usr/share/doc/tree-1.6.0/LICENSE
/usr/share/doc/tree-1.6.0/README
/usr/share/man/man1/tree.1.gz
</pre>

More about this when we come to talk about rpm.