---
ID: 312
post_title: 'Git &#8211; Setting up Git and respective config files'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/git/git-setting-up-git-and-respective-config-files
published: true
post_date: 2015-03-07 00:00:00
---
Right after installing Git, you need to configure git before you can start using it.

There are 3 files where git configuration settings are stored. These files actually stores the same info but at different scopes, repo scope, user scope, and machine level scope:


<ul>
	<li><code>/etc/gitconfig</code> - machine level</li>
	<li><code>~/.gitconfig</code> or <code>~/.config/git/config</code>  - user level</li>
	<li> config file in a Git directory (i.e., <code>.git/config</code>) of whatever repository you’re currently using. Specific to that single repository. Repo level</li>
</ul>

Note: These locations will be different on a windows machine. 

These config files have over-ride feature, where the repo-level settings has the highest power, followed by user level (aka global), and then finally machine level.  

You can read/edit these files straight from the command line. For example to get a list of all overall active git settings you do:

<pre>
[root@localhost ~]# git config --list        # this lists settings after all over-rides are evaluated.
fatal: error processing config file(s)
[root@localhost ~]# git config --list --global
fatal: unable to read config file '/root/.gitconfig': No such file or directory
[root@localhost ~]# git config --list --system
fatal: unable to read config file '/etc/gitconfig': No such file or directory
[root@localhost ~]#
</pre> 

As you can see all of the above commands have failed. That's because none of these config files exists yet. however they get generated when apply setting from the command line. The two main settings that you need to set before you can start using git are what your name and email address is. Git needs this info so that git tags all the commits with these details for everyone to keep track of who commited what. You can set the name and email-address setting like this:


<pre>
[vagrant@localhost ~]$ git config --global user.name "Sher Chowdhury"
[vagrant@localhost ~]$ git config --global user.email "Sher.Chowdhury@codingbee.net"
[vagrant@localhost ~]$
</pre>

Running the above commands generate the following file in the user's home directory where these info are stored:

<pre>
[vagrant@localhost ~]$ cat .gitconfig
[user]
        name = Sher Chowdhury
        email = Sher.Chowdhury@codingbee.net
[vagrant@localhost ~]$

</pre>


Now we can view this info from the command line:

<pre>
[vagrant@localhost ~]$ git config --list
user.name=Sher Chowdhury
user.email=Sher.Chowdhury@codingbee.net
[vagrant@localhost ~]$ git config --list --global
user.name=Sher Chowdhury
user.email=Sher.Chowdhury@codingbee.net
[vagrant@localhost ~]$ 
</pre>

Note, if you omit the "global" scope setting, then git will automatically apply the setting at the repo level, if it exists, otherwise it fails:

<pre>
[vagrant@localhost ~]$ git config user.name "Sher Chowdhury"
error: could not lock config file .git/config: No such file or directory
[vagrant@localhost ~]$
</pre>

Now here's how you do it at the system level:

<pre>
[vagrant@localhost ~]$ sudo git config --system user.email "Sher.Chowdhury@codingbee.net"
[vagrant@localhost ~]$ cat /etc/gitconfig
[user]
        email = Sher.Chowdhury@codingbee.net
[vagrant@localhost ~]$ git config --list --system
user.email=Sher.Chowdhury@codingbee.net
[vagrant@localhost ~]$

</pre>