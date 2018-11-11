---
ID: 377
post_title: 'RHCSA &#8211; Using Yum'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-using-yum
published: true
post_date: 2015-05-03 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="How can you get yum to prompt you what available sub commands are?"]
The double tab technique
[/toggle]
[toggle title="True or False, uninstalling a package using yum will also uninstall the dependencies?"]
True  - However if you have installed the package a long time ago, and since then you have installed other packages that have some of the same dependencies, then these dependencies will remain.  
[/toggle]
[toggle title="What is the command to remove the httpd package?"]
$ yum remove httpd 
[/toggle]
[toggle title="What is the command to find all available packages called 'nmap'?"]
$ yum search nmap

[/toggle]
[toggle title="What is the command to view info about the package 'nmap'?"]
$ yum info nmap

[/toggle]
[toggle title="What is the command to install the package 'nmap'?"]
$ yum install nmap

[/toggle]
[toggle title="What are the 3 commands to list all packages, installed packages, and available packages?"]
$ yum list all 
$ yum list installed 
$ yum list available
[/toggle]
[toggle title="What is the command to install all the latest packages including security updates?"]
$ yum update
[/toggle]
[toggle title="What is the command to install the 'tree' package from a repo recalled 'epel' repo rather than any other repo?"]
$ yum --enablerepo=epel install tree
[/toggle]
[toggle title="What is the command to list all package groups?"]
$ yum grouplist

[/toggle]
[toggle title="What is the command to install a the tree.rpm file which is in the current directory?"]
$ yum localinstall tree.rpm

[/toggle]
[/accordion]

<hr/>

To get an idea on what you can do with yum and how to use it, first check out it's help info:


<pre>
$ yum --help
</pre>


<h2>Install a package</h2>
The most common thing you will want to do is install a package. But first you need to see if that package is available for installation, we can check this by using yum's "search" subcommand, e.g.:

<pre>
$ yum search nmap
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: centos.hyve.com
 * epel: mirror.euserv.net
 * extras: mirror.synergyworks.co.uk
 * updates: mirror.as29550.net
======================================================== N/S matched: nmap =========================================================
nmap-frontend.noarch : The GTK+ front end for nmap
nmap-ncat.x86_64 : Nmap's Netcat replacement
nmap.x86_64 : Network exploration tool and security scanner

  Name and summary matches only, use "search all" for everything.

</pre>


The above shows us that the package is available, but it doesn't say whether or not this package is already installed. To find this out out, you need to use the "info" subcommand:


<pre>
$ yum info nmap
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: centos.hyve.com
 * epel: mirror.bytemark.co.uk
 * extras: mirror.synergyworks.co.uk
 * updates: mirror.as29550.net
Available Packages
Name        : nmap
Arch        : x86_64
Epoch       : 2
Version     : 6.40
Release     : 4.el7
Size        : 3.9 M
<strong>Repo        : base/7/x86_64</strong>
Summary     : Network exploration tool and security scanner
URL         : http://nmap.org/
Licence     : GPLv2 and LGPLv2+ and GPLv2+ and BSD
Description : Nmap is a utility for network exploration or security auditing.  It supports
            : ping scanning (determine which hosts are up), many port scanning techniques
            : (determine what services the hosts are offering), and TCP/IP fingerprinting
            : (remote host operating system identification). Nmap also offers flexible target
            : and port specification, decoy scanning, determination of TCP sequence
            : predictability characteristics, reverse-identd scanning, and more. In addition
            : to the classic command-line nmap executable, the Nmap suite includes a flexible
            : data transfer, redirection, and debugging tool (netcat utility ncat), a utility
            : for comparing scan results (ndiff), and a packet generation and response analysis
            : tool (nping).


</pre>


Here the Repo entry is the indicator of whether or not this package is installed. If it is installed , it will say "installed", otherwise it will give the source repo's name. 

Let's now go ahead and installe the package:


<pre>
$ yum install nmap
</pre>

Then confirm that it has been installed:

<pre>
$ yum info nmap | grep "^Repo"
Repo        : installed
</pre>


If you want to remove the package, then use the "remove" subcommand: 

<pre>yum remove nmap </pre>


The following Lists all the different groups of packages that are available. 


<pre>
$ yum grouplist 
Available environment groups:
   Minimal Install
   Compute Node
   Infrastructure Server
   File and Print Server
   MATE Desktop
Available Groups:
   CIFS file server
   Compatibility libraries
   Console internet tools
   Desktop
   Desktop Platform
   Desktop Platform Development
   Development Tools
   Eclipse
</pre>

“package groups” are a collection of packages that are used for a specific task/setup. E.g. if you want to set up a web server, then you should install the package group called “Web Server”


The following installs a group-package. I think that you only use double-quotes when the group package name includes spaces.

<pre>
$ yum groupinstall “{group-package-name}” 

$ yum groupremove “{group-package-name}”      # This un-installs package groups.</pre>
</pre>



<h2>listing all/installed/available packages</h2>


<pre>
$ yum list all | wc -l
16708
$ yum list installed | wc -l
1306
$ yum list available | wc -l
15345
</pre>
Note: the numbers don't add up, not sure why. 


Another way to list all installed packages, is using rpm like this:

<pre>
rpm -qa
</pre>

Note that that "yum list installed" shows slightly more lines compared to "rpm -qa", this could be because yum also lists package groups. 


Note: you could also do "yum list installed", but I think it gives slightly different info. 

Note: If the same package is available from 2 different repos, and you installed it from one repo, then the other package will still show up in the above list. Here's an example for a package called "libssh2":


<pre>[root@puppet01 ~]# yum list  | grep libssh2  # here 5 packages exists altogether
Trying other mirror.
libssh2.x86_64                         1.4.2-1.el6                    @anaconda-OracleLinuxServer-2013           11252058.x86_64/6.5
libssh2.i686                           1.4.2-1.el6                    oel_public
libssh2-devel.i686                     1.4.2-1.el6                    oel_public
libssh2-devel.x86_64                   1.4.2-1.el6                    oel_public
libssh2-docs.x86_64                    1.4.2-1.el6                    oel_public
[root@puppet01 ~]# yum list installed | grep libssh2  # ....of which one of them is installed. 
libssh2.x86_64                     1.4.2-1.el6                       @anaconda-OracleLinuxServer-201311252058.x86_64/6.5
[root@puppet01 ~]# yum list available | grep libssh2  # ....and 4 are available for install.
libssh2.i686                           1.4.2-1.el6                    oel_public
libssh2-devel.i686                     1.4.2-1.el6                    oel_public
libssh2-devel.x86_64                   1.4.2-1.el6                    oel_public
libssh2-docs.x86_64                    1.4.2-1.el6                    oel_public
[root@puppet01 ~]#</pre>






So far we have seen how to install from online repos. However if you have the actual rpm file on your machine, then you can install it locally using:



<pre>
yum localinstall packagename.arch.rpm 
</pre>
Note, this command will also install any dependencies that this package has. 

Another cool option is:

<pre>
yum whatprovides */{command name}
</pre>

This is useful if you know the command name, but not sure the name of the rpm package this command comes in. 



Sometime's you'll want to clean out all your yum repo cache, you can do this by running:

<pre>
$ yum clean all
</pre>


Here's how to install a package from a particular repo:

<pre>
$ yum --enablerepo=epel install tree
</pre>


See also:

http://www.tecmint.com/20-linux-yum-yellowdog-updater-modified-commands-for-package-mangement/