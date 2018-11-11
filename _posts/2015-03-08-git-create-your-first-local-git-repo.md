---
ID: 314
post_title: 'Git &#8211; Create your first local git repo'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/git/git-create-your-first-local-git-repo
published: true
post_date: 2015-03-08 00:00:00
---
There's 2 ways that you can get started with using git:

<ul>
	<li>Work on an existing project that is already being version-controlled with git.</li>
	<li>Create a new project that you want to version-control and share with others using Git. </li>
</ul>

A project in this context, can be anything, e.g. the source code for a java based project, or a c# project, or an android project....etc. 
  



If you are creating a brand new project, then you need to first create your git repo. If you want to work on an existing git-managed project, then you need to clone that project from the git-server, which is covered in the next lesson. 

To create a git repo, you use the "init" command

<pre>[vagrant@localhost ~]$ mkdir demo1
[vagrant@localhost ~]$ cd demo1/
[vagrant@localhost demo1]$ ls -la | grep ".git"
[vagrant@localhost demo1]$ git init
Initialized empty Git repository in /home/vagrant/demo1/.git/
[vagrant@localhost demo1]$ ls -la | grep ".git"
drwxrwxr-x  7 vagrant vagrant 4096 Mar  8 13:12 .git
[vagrant@localhost demo1]$
</pre>

At this stage this git repo doesn't know which files needs to be tracked by this repo. Hence git is not tracking anything yet. Git also will not automatically track any files/folders that sites alongside the .git folder. It is up to you to tell what files/folders needs to track. As a result you see the following when you check your git repo's status:


<pre>
[vagrant@localhost demo1]$ git status
# On branch master
#
# Initial commit
#
nothing to commit (create/copy files and use "git add" to track)
[vagrant@localhost demo1]$

</pre>





Once you have version controlled your project, you can share your project with others. The best way to do this is by pushing your repo to your companies git-server, or if you want to share it publically, then push it so a public git-service, e.g. github.com.