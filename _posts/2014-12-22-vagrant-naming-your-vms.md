---
ID: 234
post_title: 'Vagrant &#8211; Naming your VMs'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/vagrant/vagrant-naming-your-vms
published: true
post_date: 2014-12-22 00:00:00
---
You can assign a name to your VM, both in what's displayed by the vagrant output, and also on the Oracle Virtualbox gui. 


<pre>
config.vm.define :puppetmaster-abc do |puppetmaster_config|        # this names it for vagrant
  puppetmaster_config.vm.hostname = "puppetmaster.codingbee.dyndns.org"
  puppetmaster_config.vm.provider "virtualbox" do |v, override|
    v.name = "puppetmaster-def"                                     # this names it for oracle gui 
  end
end

</pre>