---
ID: 529
post_title: 'Ansible &#8211; Installing Ansible'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ansible/ansible-installing-ansible
published: true
post_date: 2016-02-03 00:00:00
---
There are a few of ways to install ansible. For the rest of these articles I'll be running Ansible on Centos 7.
<h2>Install Ansible using yum</h2>
This is done like this:
<pre>$ yum install epel-release
$ yum install ansible
</pre>
Then check the version of ansible installed:
<pre>$ ansible --version
</pre>
It is actually recommended to install ansible on all client's and just the controller. That way the clients ends up installing all the python related dependencies.

&nbsp;

However the downside with this approach is that the version of ansible that get's installed is a few versions behind the latest stable version.
<h2>Install Ansible from source</h2>
To install a newer version, you can run this script as the root user:

[github file = "/Sher-Chowdhury/vagrant-ansible/blob/master/scripts/install-ansible.sh"]

After running the above script, log into the machine as the user, "ansible" and password "vagrant".

Then check what version of ansible is installed:
<pre>[ansible@controller ~]$ ansible --version
ansible 2.1.0 (devel c433289a8b) last updated 2016/02/03 19:41:31 (GMT +100)
  lib/ansible/modules/core: (detached HEAD 93d02189f6) last updated 2016/02/03 19:41:39 (GMT +100)
  lib/ansible/modules/extras: (detached HEAD fff5ae6994) last updated 2016/02/03 19:41:51 (GMT +100)
  config file =
  configured module search path = Default w/o overrides
</pre>
<h2>Set up ansible development environment using vagrant</h2>
I created a <a href="https://github.com/Sher-Chowdhury/vagrant-ansible">ansible-vagrant project on github</a> to create your very own ansible vagrant environment. If you are new to ansible, then I recommend this approach. That's because not only will it install a recent version of ansible, but will also set up passwordless ssh access so that your controller can access both client vms without a password prompt.

Also see:

http://docs.ansible.com/ansible/intro_installation.html