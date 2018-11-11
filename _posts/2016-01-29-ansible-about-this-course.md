---
ID: 526
post_title: 'Ansible &#8211; About this Course'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ansible/ansible-about-this-course
published: true
post_date: 2016-01-29 00:00:00
---
<h2>What is Ansible</h2>
Ansible can be thought of as a multi-purpose tool for:
<ul>
	<li><strong>Cloud Infrastructure Automation</strong> - You can use it to build new virtual machines on popular cloud platforms, such as AWS and Azure. Another very popular tool that's used for this kind of work is Terraform</li>
	<li><strong>Configuration Management of Linux/Windows machines</strong> - You can use it to configure Linux and Windows Machines. This means Ansible is used to install software, update config files, and start services. </li>
	<li><strong>Deployment tool</strong> - It can be used to deploy new versions of in-house developed software to your server</li>
</ul>
&nbsp;

Ansible has a controller-client type architecture, where you have one main (aka the controller) managing lots of other machines (aka clients). Ansible is an agentless architecture which means that you only need to install Ansible on the Controller. 

The Controller uses SSH/WinRM to communicate with all the clients.    

&nbsp;
&nbsp;
<h2>Advantages of Ansible</h2>
<ul>
	<li>Easy to read syntax - All the code is written in YAML syntax</li>
	<li>It is a multi-purpose tool - it can do provisioning, environment orchestration, configuration management tool, deployment tool</li>
	<li>You don't need to install or configure anything on the clients. The clients needs to have ssh, and a relatively recent version of python</li>
	<li>Ansible pushed based - clients don't need to have any services running to periodically do an ansible run. Instead you trigger the run from the controller.</li>
	<li>Scalable - Easy to build multiple controllers, the clients are not configured to communicate with a particular controller. Hence when number of clients goes up to thousands, then you can quickly build new controllers to handle</li>
	<li>can execute adhoc shell commands on the clients</li>
	<li>Huge library of available modules -  These modules behaves idempotently to bring a ensure a stage.</li>
</ul>
&nbsp;
<h2>Disadvantages of Ansible</h2>
<ul>
	<li><strong>Abstractions are kept to a minimum</strong> -  e.g. for installing packages on RHEL based OS, you need to use the yum's built-in module, whereas for Ubuntu, you use apt's built-in module instead</li>
	<li><strong>Individual Ansible runs can be a little slow</strong> - This becomes apparent when compared to other tools such as 
</ul>


http://docs.ansible.com/ansible/