---
ID: 251
post_title: 'Puppet &#8211; Install Hiera'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-install-hiera
published: true
post_date: 2015-01-01 00:00:00
---
Before you can start using hiera, you first need to install the hiera rpm package as well as the hiera gem. You only isntall hiera on the master. However if you are managing a standalone agent, then you need to install it on these agents too. 


To check if hiera rpm is already installed do:

<pre>
[root@puppetmaster ~]# rpm -qa | grep "hiera"
hiera-1.3.4-1.el6.noarch
[root@puppetmaster ~]#
</pre>

If nothing is outputted, then you can do:

<pre>
yum install hiera
</pre>

or get puppet to install it for you:

<pre>

[root@puppetmaster ~]# puppet resource package hiera ensure=installed

</pre>



Next you need the hiera gem installed. You can check if that is so by running:

<pre>
[root@puppetmaster ~]# gem list

*** LOCAL GEMS ***

json (1.5.5)
[root@puppetmaster ~]#
</pre>

If it's not installed then do:

<pre>
[root@puppetmaster /]# gem install hiera
Successfully installed json_pure-1.8.1
Successfully installed hiera-1.3.4
2 gems installed
Installing ri documentation for json_pure-1.8.1...
Installing ri documentation for hiera-1.3.4...
Installing RDoc documentation for json_pure-1.8.1...
Installing RDoc documentation for hiera-1.3.4...
[root@puppetmaster /]#
</pre>

This also installs all dependencies. After that you should find the hiera gem is now installed:


<pre>
[root@puppetmaster /]# gem list

*** LOCAL GEMS ***

hiera (1.3.4)
json (1.5.5)
json_pure (1.8.1)
[root@puppetmaster /]#
</pre>