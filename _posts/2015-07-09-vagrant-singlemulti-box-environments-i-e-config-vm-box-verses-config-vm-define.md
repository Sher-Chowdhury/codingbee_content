---
ID: 439
post_title: 'Vagrant &#8211;  Single/Multi box environments, i.e. &#8220;config.vm.box&#8221; verses &#8220;config.vm.define&#8221;'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/vagrant/vagrant-singlemulti-box-environments-i-e-config-vm-box-verses-config-vm-define
published: true
post_date: 2015-07-09 00:00:00
---
So far we have come across:

<pre>
# -*- mode: ruby -*-
# vi: set ft=ruby :
VAGRANTFILE_API_VERSION = "2"
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
    config.vm.box ="puppetlabs/ubuntu-14.04-32-nocm"
end
</pre>

Using "config.vm.box" means that your vagrantfile is limited to be able to only spin up a single box. 

However instead of "config.vm.box" we could use the "config.vm.define"

<pre>
# -*- mode: ruby -*-
# vi: set ft=ruby :
VAGRANTFILE_API_VERSION = "2"
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.define "puppetmaster" do |puppetmaster|
    puppetmaster.vm.box ="puppetlabs/ubuntu-14.04-32-nocm"
  end
end
</pre>


this approach lets us label each box with a name that is used internally within the vagrant file, in this case I labelled it "puppetmaster". 


If you do a "vagrant up" for both of the above boxes, then the end result is the same, however with the "config.vm.define" approach it leaves the door open to add other boxes in future.

that's why it's best practice to always use the "config.vm.define" instead of "config.vm.box".

Follow along from the same pattern we end up with:


https://github.com/Sher-Chowdhury/centos7-packer-virtualbox-foreman/blob/251497d947c83cc2b0125d11d905f8cd21658bd0/Vagrantfile


Notice the "puppetmaster.vm.provider" pattern. 


Note we have replaced the word "config" with our label. But if you want to look anything up in vagrant docs for "puppetmaster.vm.provider", then you need to look for "config.vm.provider". For example search for "config.vm.provider" on the follwoing page:

http://docs.vagrantup.com/v2/vagrantfile/machine_settings.html





http://docs-v1.vagrantup.com/v1/docs/base_boxes.html