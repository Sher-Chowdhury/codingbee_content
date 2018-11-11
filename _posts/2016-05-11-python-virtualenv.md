---
ID: 581
post_title: 'Python &#8211; Virtualenv'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/python/python-virtualenv
published: true
post_date: 2016-05-11 00:00:00
---
Virtualenv is the ruby equialent of ruby's rbm.




To start using Virtualenv, you need to run the following as the root user:





<pre>
$ yum install python-pip
$ pip install virtualenv
$ pip install virtualenvwrapper  # optional but provides the workon
</pre>

From this point forward you can run all other commands as the normal user. 

By convention, we create the following folder which will house all our virtual environments, then cd into it:

<pre>
$ mkdir .virtualenvs
$ cd .virtualenvs
</pre>


Next we create the environment with the name 'testenv':

<pre>
$ virtualenv testenv
</pre>

This will create the folder in the cwd with the same name. 

Next we activate our new python virtualenv environment which we do like this:

<pre>
$ . /testenv/bin/activate
</pre>

This will end up changing your command prompt to indicate you are running in an isolated environment. To confirm this, run:

<pre>$ which python</pre>




From this point forward, any 'pip install' commands you run will install inside our testenv folder. 



to return to system level python, run:

<pre>$ deactivate</pre>

Note, when writing python code, you should do it outside of the .virtualenv folder. .virtualenv folder is mainly for internal use. 


To switch between environments, you do:

<pre>$ workon envname</pre>

Note: this needs extra config to get workon command working, including updating your .profile file