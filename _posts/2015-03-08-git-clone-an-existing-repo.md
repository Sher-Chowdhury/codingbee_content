---
ID: 315
post_title: 'Git &#8211; Clone an existing repo'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/git/git-clone-an-existing-repo
published: true
post_date: 2015-03-08 00:00:00
---
Let's say that there is an existing git repo (that is on a git server) that you want to work with. In that case you can make an entire copy of that repo using the "clone" command. 

For example, let's say I want to work with the code that is in the following repo:


https://github.com/puppetlabs/puppetlabs-ntp


On this page, you'll find the following link:

https://github.com/puppetlabs/puppetlabs-ntp.git


This is the link you need to make clone the repo. So to clone this repo, we do the following:


<pre>
[vagrant@localhost Documents]$ ls -l
total 0
[vagrant@localhost Documents]$ git clone https://github.com/puppetlabs/puppetlabs-ntp.git
Initialized empty Git repository in /home/vagrant/Documents/puppetlabs-ntp/.git/
remote: Counting objects: 1688, done.
remote: Total 1688 (delta 0), reused 0 (delta 0), pack-reused 1688
Receiving objects: 100% (1688/1688), 343.95 KiB | 350 KiB/s, done.
Resolving deltas: 100% (826/826), done.
[vagrant@localhost Documents]$ ls -l
total 4
drwxr-xr-x 8 vagrant vagrant 4096 Mar  9 14:02 puppetlabs-ntp
</pre>

As you can see the clone command downloads the whole git repo in directory by the same name. Note if you want the folder to have some other name, e.g. "ntp", then do:

<pre>
$ git clone https://github.com/puppetlabs/puppetlabs-ntp.git ntp
</pre>

In the above example we use the https protocol, but there are other protocals, e.g. ssh that you can also use, if access to a repo requires authentication. 


Now let's take a look inside this folder:

<pre>
[vagrant@localhost Documents]$ cd puppetlabs-ntp/
[vagrant@localhost puppetlabs-ntp]$ ls -la
total 104
drwxr-xr-x 8 vagrant vagrant  4096 Mar  9 14:02 .
drwxr-xr-x 3 vagrant vagrant  4096 Mar  9 14:02 ..
-rw-rw-r-- 1 vagrant vagrant  5781 Mar  9 14:02 CHANGELOG.md
-rw-rw-r-- 1 vagrant vagrant  7965 Mar  9 14:02 CONTRIBUTING.md
-rw-rw-r-- 1 vagrant vagrant   131 Mar  9 14:02 .fixtures.yml
-rw-rw-r-- 1 vagrant vagrant   900 Mar  9 14:02 Gemfile
drwxrwxr-x 8 vagrant vagrant  4096 Mar  9 14:02 .git
-rw-rw-r-- 1 vagrant vagrant    83 Mar  9 14:02 .gitignore
drwxrwxr-x 3 vagrant vagrant  4096 Mar  9 14:02 lib
-rw-rw-r-- 1 vagrant vagrant 11346 Mar  9 14:02 LICENSE
drwxrwxr-x 2 vagrant vagrant  4096 Mar  9 14:02 manifests
-rw-rw-r-- 1 vagrant vagrant  1941 Mar  9 14:02 metadata.json
-rw-rw-r-- 1 vagrant vagrant   753 Mar  9 14:02 .nodeset.yml
-rw-rw-r-- 1 vagrant vagrant   495 Mar  9 14:02 Rakefile
-rw-rw-r-- 1 vagrant vagrant  8697 Mar  9 14:02 README.markdown
drwxrwxr-x 6 vagrant vagrant  4096 Mar  9 14:02 spec
-rw-rw-r-- 1 vagrant vagrant    43 Mar  9 14:02 .sync.yml
drwxrwxr-x 2 vagrant vagrant  4096 Mar  9 14:02 templates
drwxrwxr-x 2 vagrant vagrant  4096 Mar  9 14:02 tests
-rw-rw-r-- 1 vagrant vagrant   518 Mar  9 14:02 .travis.yml
[vagrant@localhost puppetlabs-ntp]$

</pre>

Here we have all project files (as of the latest commit). Hence we see the following when we check the status:


<pre>
[vagrant@localhost puppetlabs-ntp]$ git status
# On branch master
nothing to commit (working directory clean)

</pre>

The really cool thing about the .git folder is that it house the git repo's entire history. One of the things this means is that you can disconnect your workstation from the internet, delete the project files. , but still recover them again because all the project files are stored in the ".git" folder (in compressed form), along with all it's past history. 



In other word's the .git folder essentially acts as the project's time machine. This means you can use git to roll back your project to how it looked like at some point in the past. E.g. roll back to commit number 2.