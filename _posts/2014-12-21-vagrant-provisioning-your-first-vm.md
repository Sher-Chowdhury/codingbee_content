---
ID: 231
post_title: 'Vagrant &#8211; Provisioning your first VM'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/vagrant/vagrant-provisioning-your-first-vm
published: true
post_date: 2014-12-21 00:00:00
---
1. Open up git-bash terminal and create a folder that will contain all your personal vagrant files, in my case I have created a top level folder called "vagrant":
<pre>mkdir c:\vagrant\centos</pre>
Note: I also create the centos directory in order to keep all my vagrant vms organised.

2. cd into this directory.

3. By default vagrant is designed to automatically download OS images from <a href="https://vagrantcloud.com/discover/featured">vagrantcloud</a>. This website is a repository that host hundreds of OS images. The cool thing about this is that it is free to use! On <a href="https://vagrantcloud.com/discover/featured">VagrantCloud</a>, search for an image that you are interested in (make sure you pick one that is compatible with your hypervisor, which in my case is virtualbox). In this example, I have searched for centos and will go with the following:

<a href="http://codingbee.net/wp-content/uploads/2014/10/IADphNO.png"><img class="alignnone size-full wp-image-1648" src="http://codingbee.net/wp-content/uploads/2014/10/IADphNO.png" alt="" width="1026" height="117" /></a>

Note, vagrant cloud isn't the only place to download vagrant boxes, you can also use:

- http://www.vagrantbox.es/
- http://puppet-vagrant-boxes.puppetlabs.com/ (official puppetlab boxes)

4. Make a note of the name, which in this case is "puppetlabs/centos-6.5-64-puppet"

5. Now tell vagrant you want to use this image by running the following command:
<pre>PS C:\vagrant\centos&gt; vagrant init puppetlabs/centos-6.5-64-puppet
</pre>

6. You should get the following output:
<pre>PS C:\vagrant\centos&gt; vagrant init puppetlabs/centos-6.5-64-puppet
A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.
PS C:\vagrant\centos&gt;
</pre>
Confirm the existence of the above file and take a look at it's content. We will cover a lot more about this file later.

7. While in the directory that contains vagrantfile, run the following (and then wait 10-20 mins):
<pre>vagrant up
</pre>
This should end up downloading an in OS image to:
<pre>C:\Users\{username}\VirtualBox VMs</pre>
You should get an output like this:

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

<a href="http://codingbee.net/wp-content/uploads/2014/12/vagrant-up-output.png"><img class="alignnone size-full wp-image-2374" src="http://codingbee.net/wp-content/uploads/2014/12/vagrant-up-output.png" alt="" width="677" height="616" /></a>

9. Now we can log into the vm from our git bash terminal using:

<pre>
vagrant ssh           
</pre>
This is helpful because we don't know the "vagrant" user's password. 

If you did know what the password is, then you can use an ssh client (e.g. putty) to connect to the vm. 

10. We can then switch to root like this:

<pre>
sudo su -
</pre>





11. Once you have finished with your vm, you can gracefully shutdown your vm using:
<pre>vagrant halt</pre>

12. Also you can destroy your vm like this if you no longer need it:

<pre>
vagrant destroy
</pre>

The particular image we are using above doesn't come pre-installed with the gnome gui interface. This means that you can only use this vm in command line mode. However you can reconfigure it run in gui mode which we'll cover in the next lesson.  by first isntalling gnome:

<pre> 
yum -y groupinstall "Desktop" "Desktop Platform" "X Window System" "Fonts"
</pre>

Then you can switch to the gui mode using the telinit command.

You can also change the default <a href="http://codingbee.net/tutorials/redhat3/linux-runlevels-part-1/" title="Linux - Runlevels (part 1)">runlevel</a> by updating the following file:

<pre>
vi /etc/inittab
</pre>