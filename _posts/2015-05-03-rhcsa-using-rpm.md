---
ID: 375
post_title: 'RHCSA &#8211; Using RPM'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-using-rpm
published: true
post_date: 2015-05-03 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is the command to install tree.rpm that is currently residing in the local directory?"]
$ rpm -ivh tree.rpm                
[/toggle]
[toggle title="What is the command to install tree.rpm, which resides at http://codingbee.net/tree.rpm?"]
$ rpm -ivh http://codingbee.net/tree.rpm 
[/toggle]
[toggle title="What is the command to list all packages that contains the word 'tree'?"]
$ rpm -qa | grep tree  
[/toggle]
[toggle title="What is the command to uninstall a package called 'tree'?"]
$ rpm -e tree  
[/toggle]
[toggle title="What is the command to upgrade package called tree?"]
$ rpm -Uvh tree.rpm 
[/toggle]
[toggle title="What is the command do view info data for the package 'tree'?"]
$ rpm -qi tree
[/toggle]
[toggle title="What is the command view full list of all files that have been added to our machine as part of installing the package 'tree'?"]
$ rpm -ql tree
[/toggle]
[toggle title="What is the command to locate all installed documentation that came from installing the package 'tree'?"]
$ rpm -qd tree
[/toggle]
[toggle title="What is the command to locate all config files that came from installing the package 'tree'?"]
$ rpm -qc tree
[/toggle]
[toggle title="What is the command to view the list of files that are contained within tree.rpm before installing it, assuming tree.rpm is in your current working directory?"]
$ rpm -qpl tree.rpm
[/toggle]
[/accordion]

<hr/>

Redhat Package Manager (aka rpm) is the main utility used for installings software packages. yum is actually a wrapper for RPM which has been created to make rpm easier to use. Here is how you install an RPM package that you have already downloaded:


<pre>
$ rpm -ivh {package-name}.rpm                
</pre>
Note: this isn't best practice, because it wont handle rpm dependencies. Better to do it with yum command instead. 

Here we (i)nstall the packaged in (v)erbose mode, and using a (h)ashed progress bar to show the progress. 

Note, the rpm file could exist on the internet, e.g.: 

<pre>
$ rpm -ivh http://yum.puppetlabs.com/puppetlabs-release-el-6.noarch.rpm 
</pre>


Unfortunately the above command doesn’t give any “installation successful” messages. So, after installing it, you can then check if it has been installed by running the following command:

<pre>
$ rpm -qa | grep tree  
</pre>


This rpm command (q)ueries and lists (a)ll installed packages only.  


To uninstall a package, you use the (e)rase option:


<pre>
$ rpm -e tree  
</pre>


Note, you don’t have to state the full package name to uninstall it.

After uninstalling it, you can do “rpm -qa” again to confirm that package has definitely been uninstalled.


<h2>Upgrading packages</h2>
Upgrading a package, is done like this:

<pre>
$ rpm -Uvh {package-name}.rpm 
</pre>

Here we use the (U)pgrade option to upgrade the package (if it has already been installed). If the package hasn't been installed in the first place, then the U option automatically installs the latest version. 


There is also a yum equivalent that you can try:

<pre>
$ yum update {package-name} 
</pre>


<h2>Querying installed packages</h2>

rpm lets you find various information about your installed packages. This is main reason why the rpm command is still commonly used. Rpm maintains it's own internal db that holds information about the various installed packages, and the "-q" option let's us query this db. 

Also note that the rpm command does not know anything about yum repos. Yum repos are specific to the yum utility. Hence you can't use rpm to view a list of packages that are available for installation (but not installed yet).


Here's how you can find high level (i)nformation about a package:
<pre>
$ rpm -qi tree
Name        : tree
Version     : 1.6.0
Release     : 10.el7
Architecture: x86_64
Install Date: Sat 11 Apr 2015 00:25:27 BST
Group       : Applications/File
Size        : 89505
License     : GPLv2+
Signature   : RSA/SHA256, Fri 04 Jul 2014 06:36:46 BST, Key ID 24c6a8a7f4a80eb5
Source RPM  : tree-1.6.0-10.el7.src.rpm
Build Date  : Mon 09 Jun 2014 20:28:53 BST
Build Host  : worker1.bsys.centos.org
Relocations : (not relocatable)
Packager    : CentOS BuildSystem <http://bugs.centos.org>
Vendor      : CentOS
URL         : http://mama.indstate.edu/users/ice/tree/
Summary     : File system tree viewer
Description :
The tree utility recursively displays the contents of directories in a
tree-like format.  Tree is basically a UNIX port of the DOS tree
utility.
</pre>








rpm lets you see what (d)ocumentations was installed along with the package's installation:

<pre>
$ rpm -qd tree
/usr/share/doc/tree-1.6.0/LICENSE
/usr/share/doc/tree-1.6.0/README
/usr/share/man/man1/tree.1.gz

</pre> 


Here's a command that lists all the files that have been installed from a package:


<pre>
$ rpm -ql tree
/usr/bin/tree
/usr/share/doc/tree-1.6.0
/usr/share/doc/tree-1.6.0/LICENSE
/usr/share/doc/tree-1.6.0/README
/usr/share/man/man1/tree.1.gz
</pre>
 


You can take the same approach for querying the location of all the config files that have been deployed as part of a package installation: 


<pre>
$ rpm -qc openssh-server
/etc/pam.d/sshd
/etc/ssh/sshd_config
/etc/sysconfig/sshd
</pre>




<h2>Querying downloaded RPM files</h2>

Let's say you download an rpm package from the internet:

<pre>
$  wget http://yum.puppetlabs.com/puppetlabs-release-el-7.noarch.rpm
--2015-05-04 08:52:32--  http://yum.puppetlabs.com/puppetlabs-release-el-7.noarch.rpm
Resolving yum.puppetlabs.com (yum.puppetlabs.com)... 192.155.89.90, 2600:3c03::f03c:91ff:fedb:6b1d
Connecting to yum.puppetlabs.com (yum.puppetlabs.com)|192.155.89.90|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 10160 (9.9K) [application/x-redhat-package-manager]
Saving to: ‘puppetlabs-release-el-7.noarch.rpm’

100%[==========================================================================================>] 10,160      --.-K/s   in 0s

2015-05-04 08:52:32 (115 MB/s) - ‘puppetlabs-release-el-7.noarch.rpm’ saved [10160/10160]

$ ls -l puppetlabs-release-el-7.noarch.rpm
-rw-r--r--. 1 root root 10160 Aug 26  2014 puppetlabs-release-el-7.noarch.rpm

</pre>

Now a common thing you will want to do is to see what what files this rpm will deploy should we install it. We can view this using the (p)ackage option:


<pre>
$ rpm -qpl puppetlabs-release-el-7.noarch.rpm
warning: puppetlabs-release-el-7.noarch.rpm: Header V4 RSA/SHA1 Signature, key ID 4bd6ec30: NOKEY
/etc/pki/rpm-gpg/RPM-GPG-KEY-nightly-puppetlabs
/etc/pki/rpm-gpg/RPM-GPG-KEY-puppetlabs
/etc/yum.repos.d/puppetlabs.repo
</pre>

If we omit the "p" option then rpm will only end up querying it's own internal db, rather than the local rpm file. 

Another cool thing is that you don't even need to download the rpm, you can simply do the following instead:


<pre>
$ rpm -qpl http://yum.puppetlabs.com/puppetlabs-release-el-7.noarch.rpm
warning: http://yum.puppetlabs.com/puppetlabs-release-el-7.noarch.rpm: Header V4 RSA/SHA1 Signature, key ID 4bd6ec30: NOKEY
/etc/pki/rpm-gpg/RPM-GPG-KEY-nightly-puppetlabs
/etc/pki/rpm-gpg/RPM-GPG-KEY-puppetlabs
/etc/yum.repos.d/puppetlabs.repo
</pre>

You can then use options (c)onfig files, (d)ocumentations,...etc, in the same way to query rpm files, as long as you use it in conjuction with the local (p)ackage option, as given in the above example. 



See also:

http://www.tecmint.com/20-practical-examples-of-rpm-commands-in-linux/