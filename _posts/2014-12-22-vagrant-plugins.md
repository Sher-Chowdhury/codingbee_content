---
ID: 235
post_title: 'Vagrant &#8211; Plugins'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/vagrant/vagrant-plugins
published: true
post_date: 2014-12-22 00:00:00
---
vagrant plugin install <a href="https://github.com/adrienthebo/vagrant-hosts">vagrant-hosts</a>
vagrant plugin install <a href="https://github.com/tmatilai/vagrant-proxyconf">vagrant-proxyconf</a>


Behind the scenes these plugins get installed in:
C:\Users\{username}\.vagrant.d

Or:

C:\.vagrant.d   (that I think is if you are running git bash as admin user)


http://priyaaank.tumblr.com/post/50707609769/snapshotting-vagrant