---
ID: 230
post_title: 'Vagrant &#8211; Configuring your VM'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/vagrant/vagrant-configuring-your-vm
published: true
post_date: 2014-12-21 00:00:00
---
So far we succesfully started up our machine, but we encountered a few problems. 


The vagrantfile holds all the configurations of our vm. It holds this data in the form of an object that's been instantiated from the <a href="https://docs.vagrantup.com/v2/vagrantfile/machine_settings.html">config.vm</a> class. 

This section has number attributes that you can set, this attributes are effectively the vm's settings. 


<h2>Resolve the timeout warning
</h2>

According to the documentation, we can fix this by increasing the config.vm.boot_timeout setting: In my case I inserted the following in the block:
<pre>
config.vm.boot_timeout = 500</pre>