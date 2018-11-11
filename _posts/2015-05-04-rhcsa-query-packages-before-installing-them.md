---
ID: 378
post_title: 'RHCSA &#8211; Query packages before installing them'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-query-packages-before-installing-them
published: true
post_date: 2015-05-04 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is the command to download a package called 'tree' without installing it?"]
$ yum install tree    # followed by the 'd' option when prompted.
[/toggle]
[toggle title="What is the command to query the list of files stored inside the file 'tree.rpm'?"]
$ rpm -qpl tree.rpm

[/toggle]
<hr/>


In the last lesson we saw that the rpm command can be used to query installed packages, or individuel rpm files.

However the rpm command doesn't know anything about yum repos, so you can't use rpm to look up info about a package that is available in a yum repo (but hasn't been installed yet). 


The second approach is the better approach. That's becuase you can use yum to just download a .rpm file from a repo, then using all the power of rpm (with the "p" option enabled) to query that file. 


To download an rpm, all you need to do is use yum's install command, and then select the (d)ownload option when prompted (instead of the yes or no options):



<pre>
$ find /var/cache/ -name tree*              # quick check that .rpm doesn't exist yet. 
$ yum install tree
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: centos.hyve.com
 * epel: mirror.euserv.net
 * extras: mirror.synergyworks.co.uk
 * updates: mirror.as29550.net
Resolving Dependencies
--> Running transaction check
---> Package tree.x86_64 0:1.6.0-10.el7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

======================================================================================================
 Package             Arch         Version           Repository          Size
======================================================================================================
Installing:
 tree                x86_64       1.6.0-10.el7      base                46 k

Transaction Summary
======================================================================================================
Install  1 Package

Total download size: 46 k
Installed size: 87 k
<strong color='red'>Is this ok [y/d/N]: d</strong>
Background downloading packages, then exiting:
tree-1.6.0-10.el7.x86_64.rpm                                             |  46 kB  00:00:00
<strong>exiting because "Download Only" specified</strong>
$ find /var/cache/ -name tree*
/var/cache/yum/x86_64/7/base/packages/tree-1.6.0-10.el7.x86_64.rpm


</pre>


As you can see, when you select yum's download option, it downloads the file somwhere inside the <code>/var/cache</code> directory. 

Now we cna query the file using rpm (along with the "p" option)
<pre>
$ rpm -qpl /var/cache/yum/x86_64/7/base/packages/tree-1.6.0-10.el7.x86_64.rpm
/usr/bin/tree
/usr/share/doc/tree-1.6.0
/usr/share/doc/tree-1.6.0/LICENSE
/usr/share/doc/tree-1.6.0/README
/usr/share/man/man1/tree.1.gz
$ rpm -qpd /var/cache/yum/x86_64/7/base/packages/tree-1.6.0-10.el7.x86_64.rpm
/usr/share/doc/tree-1.6.0/LICENSE
/usr/share/doc/tree-1.6.0/README
/usr/share/man/man1/tree.1.gz
</pre>