---
ID: 3844
post_title: Comparison between Ansible and Puppet
author: sher
post_excerpt: >
  This is just a quick summary of Ansible
  and Puppet.
layout: post
permalink: >
  http://codingbee.net/tutorials/ansible/comparison-between-ansible-and-puppet
published: true
post_date: 2018-08-12 09:22:01
---
Key differences between Ansible and Puppet:
<ul>
	<li>No single master server. You can simply install ansible on any server and that can then act as the muster. In Puppet, that's not possible because the puppetmasters url is hardcoded into each puppet agent's puppet.conf file.</li>
	<li>Orchestration is done via the "push" system rather than the "pull" system. I.e. you don't need to log into ansible client and then trigger a ansible run.</li>
	<li>The code   in a playbook is executed in the order that they are specified. Where that is not the case with puppet, where if you want you can specify ordering using the before/require metaparameters</li>
	<li>Puppet is based on ruby. However Ansible is based on python, and you can write python code right into your ansible code. Python comes with a huge amount of functions/libraries that you can utilise in your ansible code.</li>
	<li>puppet code is written in it's own custom language, i.e. Domain specific   Language (DSL). However ansible code is written in yaml syntax.</li>
</ul>

This is just a quick summary of the two tools....