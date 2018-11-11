---
ID: 159
post_title: 'Vagrant &#8211; What is Vagrant'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/vagrant/vagrant-what-is-vagrant
published: true
post_date: 2014-10-18 00:00:00
---
Sometimes you'll want to try things out on your machine but you're reluctant to do so because it might end up breaking your workstation. 

One solution is to use virtualbox to create a virtual machine that runs inside your workstation. You can then do your experiments inside your vm, and if you end up breaking it, then you can simply recreate the vm. 

There are a couple of downsides with this approach: 

<ul>
	<li>Creating a vm involves performing a number of repetitive tasks to get the vm configured to how it was before. Constantly doing these tasks can get tedious.</li>
	<li>When creating a VM you need to specify quite a lot of settings, so you need to remember them, or have them written down somewhere.</li>
	<li>Sharing a vm with colleagues is not that easy.</li>
	<li>If you work in a team, then each member might setup their vm differently to each other. This means that any products you develop in your vm, might not work in someone else's vm</li>

</ul>

  

That's where vagrant comes to the rescue. Vagrant is a command-line utility that acts as a wrapper around virtualbox and can rapidly automate the VM creation process.