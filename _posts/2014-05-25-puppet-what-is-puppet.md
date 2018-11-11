---
ID: 104
post_title: 'Puppet &#8211; What is Puppet'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-what-is-puppet
published: true
post_date: 2014-05-25 00:00:00
---
Puppet is a solution that lets you control whats installed/configured on multiple machines from a single central machine. This single central machine is referred to as the "Puppet Master", and the machines it controls are called "Puppet Agents" (aka nodes or slaves).

To follow along with this tutorial series, you first need to set up your own puppet sandpit environment, this should comprise of 2 or more machines, one of which acts as the puppet master, and the rest act as puppet agents. <a title="Puppet - Install and set-up the Puppet Master and it’s Agents" href="http://codingbee.net/tutorials/puppet/puppet-crash-course-setting-masteragent-relationship/">Installing and setting up the Puppet Master and it's Agents</a> is covered in the next lesson.

In a puppetised environment, each agent checks-in with the puppet-master to see if it is set up the way it's supposed to. If the answer is yes then nothing happens, if not, then the puppet master tells the agent what it should look like, then the node makes changes to itself accordingly to reflect this.

The way this works is that on the puppet master you can define a "Desired State" for each of your nodes.   This desired state is declared in a syntax similar to a hash table's syntax.

If a node doesn't resemble a desired state, then we say that a "drift" has occurred.   A drift is identified every 30 minutes, where the following happens:
<ol>
	<li>The node sends it current setup info (referred to as "FACTS") to the Puppet master.</li>
	<li>The Puppet master uses the facts to compile a "catalog". This catalog contains detailed data about how the node should be configured.</li>
	<li>The Puppet master sends the catalog back to the node.</li>
	<li>The node enforces the changes as described in the the catalog</li>
	<li>The node then sents a "Report" back to the puppet master. You can view these reports and integrate them with other systems.</li>
</ol>
In the next lesson we'll walk through how to set up your own puppet environment which you can then experiment on to learn puppet.




Useful Links
<a href="http://docs.puppetlabs.com/learning/ral.html">
http://docs.puppetlabs.com/learning/ral.html</a> - really good intro to puppet

&nbsp;

<a href="https://docs.puppetlabs.com/puppet/latest/reference/index.html">https://docs.puppetlabs.com/puppet/latest/reference/index.html </a>- this is a the full puppet reference manual.

&nbsp;

http://www.planetpuppet.org/ - really good puppet blog aggregator.