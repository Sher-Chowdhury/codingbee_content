---
ID: 380
post_title: 'RHCSA &#8211; virsh and virt-manager'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-virsh-and-virt-manager
published: true
post_date: 2015-05-04 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is the command to enter the vm managing's interactive shell?"]
$ virsh
virsh #
[/toggle]
[toggle title="What is the command to list all VMs?"]
$ virsh
virsh # list --all
[/toggle]
[toggle title="What is the command to list only running VMs?"]
$ virsh
virsh # list
[/toggle]
[toggle title="How can you make it easier to using this interactive shell?"]
tab+tab technique
[/toggle]
[toggle title="What is the command to start a vm called 'puppetmaster'?"]
$ virsh
virsh # start puppetmaster
[/toggle]
[toggle title="What is the command to shutdown the vm called 'puppetmaster'?"]
$ virsh
virsh # destroy puppetmaster
[/toggle]
[toggle title="Which directory houses each vm's xml config file?"]
/etc/libvirt/qemu
[/toggle]
[toggle title="What is the command to start the gui for managing VMs?"]
$ virt-manager
[/toggle]
[toggle title="What is the command to edit an existing vm called 'puppetmaster'?"]
virsh # edit puppetmaster

[/toggle]
[toggle title="What is the command?"]


[/toggle]
[/accordion]

<hr/>


virsh is it's own virtual interactive shell which is triggered by simply running the virsh command:


<pre>
$ virsh
Welcome to virsh, the virtualisation interactive terminal.

Type:  'help' for help with commands
       'quit' to quit

virsh # quit

$
</pre>

Tip: When inside a virsh session, use tab+tab technique to autocomplete as much as you can to make life easier.  

Here's how to list all your vm's (both running and switched off):


<pre>
virsh # list --all
 Id    Name                           State
----------------------------------------------------


</pre>


If you just want to view a list of vms that are currently running, then simply omit the "-all" flag and do:


<pre>
virsh # list
 Id    Name                           State
----------------------------------------------------


</pre>


To start a vm, do:


<pre>
virsh # start {machine-name}
</pre>


To stop a machine do:


<pre>
virsh # destroy {machine-name}
</pre>

This doesn't actually delete the vm, it only powers it down, and hence can be started up again later. 

The entire configuration of a vm is stored as an xml file, which is named after the vm's name, and these xml config files are all stored in the following directory:


<pre>
/etc/libvirt/qemu
</pre>

If you want to make changes to one of these xml files, the you should do it via the virsh command like this:


<pre>
virsh # edit {machine-name}
</pre>

That is so that libvirt doesn't edit the xml config file at the same time as you are editing it. 


<h2>virt-manager</h2>

virt-manager is a gui tool that let's you create/manage/destroy vms. It's similar to the way virtualbox is used. 

You can start virt-manager GUI like this:


<pre>
$ virt-manager
</pre>