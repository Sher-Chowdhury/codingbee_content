---
ID: 284
post_title: 'RVM &#8211; Using rvmsudo instead of sudo'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/rvm-using-rvmsudo-instead-of-sudo
published: true
post_date: 2015-01-25 00:00:00
---
There are times when you need to run a ruby related command to install something, e.g. "bundle install" or "gem install gem-name". If you are an rvm managed ruby version, then it is bad/messy practice to do this. because doing so will install the gem system-wide, rather then ruby version level. This all comes down to an environment variable called "rvm_path". When using sudo, path no longer becomes available. That's why to get around this, we use rvmsudo instead, which retains the user's environment data while in privileged mode

<pre> 
[sher@puppetmaster ~]$ env | grep "^rvm_path"
rvm_path=/home/sher/.rvm
[sher@puppetmaster ~]$ sudo env | grep "^rvm_path"      # notice that there's no match here.
[sher@puppetmaster ~]$ rvmsudo env | grep "^rvm_path"
rvm_path=/home/sher/.rvm
[sher@puppetmaster ~]$
</pre>

Hence the gem is installed system wide.