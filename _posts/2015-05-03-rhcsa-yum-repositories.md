---
ID: 376
post_title: 'RHCSA &#8211; Yum Repositories'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-yum-repositories
published: true
post_date: 2015-05-03 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is the command to list all configured yum repos?"]
$ yum repolist
[/toggle]
[toggle title="In which directory are all the repos housed in?"]
/etc/yum.repos.d/
[/toggle]
[toggle title="What is the naming convention of a yum repo file?"]
It can be called anything you like, but the file name must contain the ".repo" suffix

[/toggle]
[toggle title="What are the entries of a .repo file?"]<span style="font-size:13px">[repo-name]                                                 # This stanza's name. This must be unique. 
name=Local Network Yum Repo               # The repo's name, as displayed by "yum repolist"
baseurl=ftp://192.168.75.132/pub/    # The directory that contains the repo's repodata file
enabled=1                          # Optional: tells yum whether to use this repo, 1=yes, 0=no
gpgcheck=0                         # whether GPG check is enabled. 1=yes, 0=no
gpgkey=file:<mark>///</mark>etc/pki/rpm-gpg-RPM-GPG-KEY-redhat-release    # Required only if gpgcheck=1</span>
[/toggle]
[toggle title="What are the http, ftp and local forms of the baseurl entry?"]
baseurl = http://servername/my/repo     
baseurl = ftp://servername/my/repo      
baseurl = file:<mark>///</mark>srv/my/repo/       
[/toggle]
[toggle title="What is the command to list all repos including the disabled ones?"]
$ yum repolist all

[/toggle]
[toggle title="What is the command to create a new repo from the command line?"]<span style='font-size:14px'>$ yum-config-manager <span style="letter-spacing:0.1px">-</span>-add-repos=http://download.fedoraproject.org/pub/epel/7/$basearch</span>
[/toggle]
<hr/>


In windows if you want to install a software, e.g. firefox, then you open up an internet browser and download it from somewhere on the internet. 

However for RHEL, we take a different approach. We have to configure our machine to use remote yum repositories.

Yum uses pre-configured online repositories (that are in the form of websites and ftp sites, or even local folders containing rpm files) to search for and install software packages. Yum can also be used to install software that have been downloaded….and it will still be able automatically install dependencies.


These yum repos can hosts thousands of open source software packages that are in the form of .rpm files. Once we have configured to use an rpm repo, we then have access to all their rpm packages, and can then install them using the "yum" command. 

First off, let's see what yum repos we currently have access to, which we do using yum's repolist option:


<pre>
$ yum repolist
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: centos.hyve.com
 * epel: mirror.bytemark.co.uk
 * extras: mirror.synergyworks.co.uk
 * updates: mirror.as29550.net
repo id                  repo name                                           status
!base/7/x86_64           CentOS-7 - Base                                     8,652
!extras/7/x86_64         CentOS-7 - Extras                                      84
!updates/7/x86_64        CentOS-7 - Updates                                    362
repolist: 16,848
</pre>
 

The config files that makes yum aware and access all these yum repos, are stored in <code>/etc/yum.repos.d</code> directory:


<pre>
$ ls -l /etc/yum.repos.d/
total 52
-rw-r--r--. 1 root root 1664 Mar 31  2015 CentOS-Base.repo
-rw-r--r--. 1 root root 1309 Mar 31  2015 CentOS-CR.repo
-rw-r--r--. 1 root root  649 Mar 31  2015 CentOS-Debuginfo.repo
-rw-r--r--. 1 root root  290 Mar 31  2015 CentOS-fasttrack.repo
-rw-r--r--. 1 root root 1331 Mar 31  2015 CentOS-Sources.repo
-rw-r--r--. 1 root root 1002 Mar 31  2015 CentOS-Vault.repo
-rw-r--r--. 1 root root 1023 Sep 16 19:45 epel.repo
-rw-r--r--. 1 root root 1056 Nov 25  2014 epel-testing.repo
-rw-r--r--. 1 root root  364 Sep 11 10:59 foreman-plugins.repo
-rw-r--r--. 1 root root  334 Sep 11 10:59 foreman.repo
-rw-r--r--. 1 root root 1250 Aug 25  2014 puppetlabs.repo
-rw-r--r--. 1 root root  158 Sep 11  2014 rhscl-ruby193-epel-7-x86_64.repo
-rw-r--r--. 1 root root  159 Jun 18 11:34 rhscl-v8314-epel-7-x86_64.repo
</pre>

If you want to access a new yum repo, then you need to create a ".repo" file in this directory. Yum will then scan this directory and load in all the .repo files. 

You can name the .repo file to your liking, .e.g whatever.repo. But it's best practice to choosing something sensible. 

The .repo file has to contain at least 4 lines:


<pre>
<span style="font-size:12px">
$ cat localnet.repo
[localnet]                                      # This stanza's name. You can choose any name, but it must be unique. 
name=Local Network Yum Repo               # The repo's name, as displayed by "yum repolist"
baseurl=ftp://192.168.75.132/pub/    # The directory that contains the repo's repodata
enabled=1                          # Optional: tells yum whether to use this repo, 1=yes, 0=no
gpgcheck=0                         # whether GNU Privacy Guard check is enabled. 1=yes, 0=no
gpgkey=file:<mark>///</mark>etc/pki/rpm-gpg-RPM-GPG-KEY-redhat-release    # Required only if gpgcheck=1
</span>
</pre>

This repodata file contains info about location and groupings of packages that yum uses to find and install. 

The most important setting in the .repo file is the baseurl. This can take the form of:

<pre>
baseurl = http://servername/my/repo   
baseurl = ftp://servername/my/repo     
baseurl = file:///srv/my/repo/             
</pre>

Notice the 3 forward slashes, when declaring a "file" baseurl. The first 2 slashes are part of the standard syntax, e.g. "http://" and "ftp://", whereas the third slash represent's your local machine's root directory. 

Note, there are some rpm that you can install, whose sole purpose is to place is to setup+enable a yum repo, i.e. place the .repo file and gpg keys. <a href="https://docs.puppetlabs.com/guides/install_puppet/install_el.html">Installing puppet software</a> is an example of this. After that you can then use yum to do the actual puppet insstallation.  


Once you have created the .repo file, you can then use yum repolist to confirm that it exists. Here's an example:


<pre>
$ touch /etc/yum.repos.d/test.repo
[root@localhost yum.repos.d]# vi /etc/yum.repos.d/test.repo
[root@localhost yum.repos.d]# cat /etc/yum.repos.d/test.repo
[mytestrepo]
name=CodingBee repo
baseurl=https://yum.puppetlabs.com/el/7/products/x86_64/
enabled=1
gpgcheck=0
[root@localhost yum.repos.d]# yum repolist
Loaded plugins: fastestmirror, langpacks
repo id                     repo name                     status
base/7/x86_64               CentOS-7 - Base                8,652
epel/x86_64                 Extra Packages for OEL         7,760
extras/7/x86_64             CentOS-7 - Extras                 84
<strong>mytestrepo                  CodingBee repo                   162</strong>
updates/7/x86_64            CentOS-7 - Updates               362
repolist: 17,020

</pre>

Note: you can do <code>yum repolist all</code> to view all repos, including those that are currently disabled. 


<h2>Using the yum-config-manger</h2>
Manually creating a .repo file can be tedious. Luckily there is a way to auto generate the .repo using <strong>yum-config-manger</strong>. All this command needs is the base url. Here's an example: 

<pre>
$ yum-config-manager --add-repos=http://download.fedoraproject.org/pub/epel/7/$basearch
</pre>

All this command will do is create the .repo file. So for this example, it created:

<pre>
$ cat /etc/yum.repos.d/download.fedoraproject.org_pub_epel_7_.repo

[download.fedoraproject.org_pub_epel_7_]
name=added from: http://download.fedoraproject.org/pub/epel/7/
baseurl=http://download.fedoraproject.org/pub/epel/7/
enabled=1
</pre>


You can also user yum-config-manager to enable/disable repos. Or you can disable/enable the repos by manually editing the .repo file. 










Also see:

[bash]
man yum.conf        #this describes the yum.conf file and the various option available.
[/bash]



<strong>Need to learn more about:</strong>

http://www.tecmint.com/20-practical-examples-of-rpm-commands-in-linux/

<a href="http://yum.baseurl.org/wiki/RepoCreate">http://yum.baseurl.org/wiki/RepoCreate</a>

<a href="http://www.cyberciti.biz/tips/redhat-centos-fedora-linux-setup-repo.html">http://www.cyberciti.biz/tips/redhat-centos-fedora-linux-setup-repo.html</a>

<a href="http://superuser.com/questions/451298/how-do-i-create-yum-repo-file">http://superuser.com/questions/451298/how-do-i-create-yum-repo-file</a>