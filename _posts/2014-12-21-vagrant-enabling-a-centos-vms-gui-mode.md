---
ID: 232
post_title: 'Vagrant &#8211; Enabling a CentOS VM&#8217;s gui mode'
author: sher
post_excerpt: >
  Most vagrant boxes comes without the gui
  desktop interface. This tutorial will
  show you how install and access to that
  gui interface.
layout: post
permalink: >
  https://codingbee.net/tutorials/vagrant/vagrant-enabling-a-centos-vms-gui-mode
published: true
post_date: 2014-12-21 00:00:00
---
Most vagrant boxes comes without the gui desktop interface. This tutorial will show you how install and access to that gui interface. Let's use the standard CentOS 7 vm:

<pre>
$ vagrant init centos/7
</pre>

The above command will create a Vagrantfile. 

Next open up the vagrant file and ensure the following <a href="https://docs.vagrantup.com/v2/virtualbox/configuration.html">virtualbox setting</a> section exists:

<pre>
config.vm.provider "virtualbox" do |v|
  v.gui = true
  v.memory = 2048
  v.cpus = 2
end


</pre>


Log into your vm:

<pre>$ vagrant ssh</pre>

Then switch to root:

<pre>$ sudo -i </pre>

Then install the gui desktop collection of packages:


<pre>
$ yum groupinstall -y 'gnome desktop'
$ yum install -y 'xorg*'
</pre>

Next uninstall the following packages:

<pre>
yum remove -y initial-setup initial-setup-gui
</pre>
These packages are to do with agreeing to EULA agreements, which means it requires user interaction, which can prevent automated startups via vagrant. 

Next switch to the gui target:

<pre>
$ systemctl isolate graphical.target
$ systemctl set-default graphical.target   # to make this persistant
</pre>

You should now see the gui desktop in your virtualbox window. If not, then try restarting the box:


<pre>
$ vagrant halt ; vagrant up
</pre>

 
I've also created the <a href="https://gist.github.com/Sher-Chowdhury/0f96ee71fcec5b3b5c205bc277a93f6b">vagrant enable gui shell script</a> that you can copy into your vagrant projects.