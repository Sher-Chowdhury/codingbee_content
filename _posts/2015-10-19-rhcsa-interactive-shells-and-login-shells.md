---
ID: 470
post_title: Interactive shells and login shells
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-interactive-shells-and-login-shells
published: true
post_date: 2015-10-19 00:00:00
---
See also <a href="http://codingbee.net/tutorials/rhcsa/rhcsa-starting-a-login-shell-or-interactive-shell-using-the-switch-user-su-command">switching users using su</a>

Whenever a new user is created, by default, a set of (usually hidden) config files will get copied to the user’s new home folder. These files are actually duplicate of all the files contained in the <code>/etc/skel</code> directory:


<pre>
$ ls -la /etc/skel/
total 24
drwxr-xr-x.  2 root root   62 Feb  2 13:31 .
drwxr-xr-x. 78 root root 8192 Mar 28 14:09 ..
-rw-r--r--.  1 root root   18 Sep  6  2017 .bash_logout
-rw-r--r--.  1 root root  193 Sep  6  2017 .bash_profile
-rw-r--r--.  1 root root  231 Sep  6  2017 .bashrc
</pre>

the <code>useradd</code> commands automatically copies all the content from this folder and places it in the user’s home directory.

when a user logs into a RHEL machine (using username and password), the following scripts are executed automatically (in this order):

/etc/profile            # (this sets up user env variables, e.g. path, hostname, histsize….etc)…this script will also trigger:

/etc/profile.d         # (directory containing lots of other files.

{user’s home folder}/.bash_profile # can be used to add your own stuff including overriding what was set in the /etc/profile script.

{user’s home folder}/.bashrc        # (used for setting local variables e.g. the PS1 variables)


However these does get executed when a non-login shell (aka interactive shell) is generated. a interactive shell is a shell that gets opened but without getting a username/password prompt. Here are some ways to start a non-login shell:

-                 Applications=&gt;system tools=&gt;terminal

-               When you start a shell script, linux will automatically create a new shell for that script to run in, and this new shell is a non-login shell.

-                 At the command line, type ksh, followed by bash.

In a non-login shell the following scripts are run:

&nbsp;

1. {user’s home folder}/bashrc,

2. /etc/bashrc,

3. /etc/profile.d (have a look through these script to familiarize yourself)




[post-content post_name=rhsca-quiz]

[accordion]
[toggle title="What are three possible commands to start a full login-shell for the user called david?"]
$ su - david     # note: option l is implicitedly assumed here.   
# or 
$ su -l david
# or 
$ ssh david@centos7machine

[/toggle]
[toggle title="Which shell scripts gets triggered as part of this command?"]
-  /etc/profile        # which in turn calls all scripts inside /etc/profile.d folder, and then it calls:
-  ~/.bash_profile    # (If for any reason this doesn't exist, then it will look for ~/.profile) which in turn calls:
-  ~/.bashrc   # which in turn calls:
-  /etc/bashrc   

[/toggle]
[toggle title="While logged in as the root user, What is the command to start a non-login shell for the user called david?"]
$ su david
# or:
$ bash
[/toggle]
[toggle title="Which shell scripts gets triggered as part of this command?"]
-  ~/.bashrc   # which in turn calls:
-  /etc/bashrc
Note: all the profile related scripts are ignored. Hence privilege is set but environment variable are unset.    
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