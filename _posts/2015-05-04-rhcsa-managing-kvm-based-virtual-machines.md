---
ID: 379
post_title: 'RHCSA &#8211; Managing KVM based Virtual Machines'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-managing-kvm-based-virtual-machines
published: true
post_date: 2015-05-04 00:00:00
---
<h2>KVM overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is the command to install all the vm related packages?"]
$ yum install \
virt-manager \
libvirt \
libvirt-client \
libvirt-python \
python-virtinst
qemu-kvm \
qemu-img \
[/toggle]
[toggle title="What is the command to enable the kernel's virtualisation feature, and how do check for it?"]
$ modprobe kvm
$ lsmod | grep kvm
kvm                   461126  0
[/toggle]
[toggle title="What is the command to start+enable the virtualization related service?"]
$  systemctl start libvirtd
$  systemctl enable libvirtd
[/toggle]
[toggle title="What are the three main utilities that interacts with the above sevice?"]
- virt-manager   # open gui tool
- virsh          # cli tool for managing tools
- virt-install   # cli alternative to gui tool
[/toggle]
[toggle title="What is the command?"]


[/toggle]
[toggle title="What is the command?"]


[/toggle]
[toggle title="What is the command?"]


[/toggle]
[toggle title="What is the command?"]


[/toggle]
[toggle title="What is the command?"]


[/toggle]
[toggle title="What is the command?"]


[/toggle]
[/accordion]

<hr/>




<h2>Hardware requirements</h2>

kvm is what reserves cpu resources and use the reserved cpu resources to hosts guest vms on the host machine. KVM allows virtualisation to happen. 

If your RHEL machine is a vm, e.g. your machine is running inside a virtualbox, then you can't run KVM inside it. Hence the first requirement is that you have to have a physical machine that's running RHEL. 

Another requirement is that your machine cpu must support the following features: 

<pre>
$ egrep 'vmx|svm' /proc/cpuinfo
</pre>

Either vmx or svm flag needs to exist. 

Most most modern cpu's support this. If your's don't then it's likely that you're using a machine that has a rather old cpu. 

<h2>Required packages</h2>

You need to use yum to install the following packages:

<ol>
	<li>virt-manager - this gives us the gnome based user interface for managing VMs. You can access this by going to <code>Applications -> System tools -> Virtual Machine Manager</code>. This utility interacts with with the libvirtd daemon. This get's installed as a dependency when installing libvirt</li>
	<li>libvirt - this is a middleman that allows virt-manage/virsh to interact with qemu+kvm</li>
	<li>qemu-kvm</li>
	<li>qemu-img</li>
	<li>libvirt-python</li>
	<li>python-virtinst</li>
	<li>libvirt-client - this provides a commandline alternative (called virsh) to virt-manager.</li>

</ol>




<pre>
$ yum install virt-manager libvirt qemu-kvm qemu-img libvirt-python python-virtinst libvirt-client
</pre>




<h2>kernel requirements</h2>

You need to enable the following kernal module:


<pre>
$ lsmod | grep kvm   
$ modprobe kvm
$ lsmod | grep kvm
kvm                   461126  0
</pre>




<h2>The libvirtd service</h2>
The libvirtd is a service that manages virtualization in general, and is not just limited to kvm virtualization. 

To manage vms, this service needs to be running+enabled:

<pre>
$  systemctl status libvirtd -l
libvirtd.service - Virtualization daemon
   Loaded: loaded (/usr/lib/systemd/system/libvirtd.service; enabled)
   Active: active (running) since Mon 2015-05-04 14:06:49 BST; 18s ago
     Docs: man:libvirtd(8)
           http://libvirt.org
 Main PID: 2289 (libvirtd)
   CGroup: /system.slice/libvirtd.service
           └─2289 /usr/sbin/libvirtd

May 04 14:06:49 localhost.localdomain libvirtd[2289]: libvirt version: 1.2.8, package: 16.el7_1.2 (CentOS BuildSystem <http://bugs.centos.org>, 2015-03-26-23:17:42, worker1.bsys.centos.org)
May 04 14:06:49 localhost.localdomain libvirtd[2289]: Module /usr/lib64/libvirt/connection-driver/libvirt_driver_lxc.so not accessible
May 04 14:06:49 localhost.localdomain systemd[1]: Started Virtualization daemon.

</pre>

The libvirtd service can be managed by the following tools:


<ul>
	<li><strong>virt-manager</strong> - This is a GUI that allows you to easily manage vms</li>
	<li><strong>virsh</strong> - short for virtualization shell. It let's you manage vms using scripts. Really useful when managing multiple VMs.</li>
	<li><strong>virt-install</strong> -lets you create vms. It's a cli alternative to virt-manager</li>
</ul>