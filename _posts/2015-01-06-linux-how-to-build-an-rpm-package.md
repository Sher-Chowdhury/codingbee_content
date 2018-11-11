---
ID: 261
post_title: 'Linux &#8211; How to build an RPM package'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/uncategorized/linux-how-to-build-an-rpm-package
published: true
post_date: 2015-01-06 00:00:00
---
To do this, you need to use the <strong>rpmbuild</strong> command.

First you have to install this utility by installing the following package:
<pre>
$ yum install rpmdevtools
</pre>

And maybe also the following too:

<pre>
$ yum install gcc make rpm-build patch
# the following I think are optional:
$ yum pcre-devel openssl-devel rrdtool-devel
</pre>

You might need to do some patching:


<pre>
$ patch -p1 --dry-run < /path/to/patch.patch
$ patch -p1 < /path/to/patch.patch</pre>

For more info about checkout out:

http://www.thegeekstuff.com/2014/12/patch-command-examples/

<pre>
$ yum info patch
Loaded plugins: rhnplugin, security, ulninfo
*Note* Spacewalk repositories are not listed below. You must run this command as root to access Spacewalk repositories.
Installed Packages
Name        : patch
Arch        : x86_64
Version     : 2.6
Release     : 6.el6
Size        : 172 k
Repo        : installed
From repo   : anaconda-OracleLinuxServer-201507280245.x86_64
Summary     : Utility for modifying/upgrading files
URL         : http://www.gnu.org/software/patch/patch.html
License     : GPLv2+
Description : The patch program applies diff files to originals.  The diff command
            : is used to compare an original to a changed file.  Diff lists the
            : changes made to the file.  A person who has the original file can then
            : use the patch command with the diff file to add the changes to their
            : original file (patching the file).
            :
            : Patch should be installed because it is a common way of upgrading
            : applications.

</pre>

Next run rpmdev-setuptree, this will result in the rpmbuild folder being created:
<pre>[root@puppetmaster ~]# ls
anaconda-ks.cfg  install.log  install.log.syslog
[root@puppetmaster ~]# rpmdev-setuptree 
[root@puppetmaster ~]# ls
anaconda-ks.cfg  install.log  install.log.syslog  rpmbuild
[root@puppetmaster ~]# tree rpmbuild/
rpmbuild/
├── BUILD
├── RPMS
├── SOURCES
├── SPECS
└── SRPMS

5 directories, 0 files
[root@puppetmaster ~]# 
</pre>

http://tecadmin.net/create-rpm-of-your-own-script-in-centosredhat/#
http://www.thegeekstuff.com/2015/02/rpm-build-package-example/

https://www.google.co.uk/search?q=rpm+setuptree&ie=utf-8&oe=utf-8&gws_rd=cr&ei=iqy9Vs6LMcve6QTXiLGQBw

http://www.cyberciti.biz/tips/building-a-source-rpm-using-rpmbuild-command.html

https://fedoraproject.org/wiki/How_to_create_an_RPM_package

https://www.google.co.uk/search?q=fpm&amp;ie=utf-8&amp;oe=utf-8&amp;aq=t&amp;rls=org.mozilla:en-GB:official&amp;client=firefox-a&amp;channel=sb&amp;gfe_rd=cr&amp;ei=TksAVYqBJsve-gbMzoCgDg#safe=off&amp;rls=org.mozilla:en-GB:official&amp;channel=sb&amp;q=fpm+rpm

https://www.google.co.uk/search?q=fpm&amp;ie=utf-8&amp;oe=utf-8&amp;aq=t&amp;rls=org.mozilla:en-GB:official&amp;client=firefox-a&amp;channel=sb&amp;gfe_rd=cr&amp;ei=TksAVYqBJsve-gbMzoCgDg#safe=off&amp;rls=org.mozilla:en-GB:official&amp;channel=sb&amp;q=maven+rpm