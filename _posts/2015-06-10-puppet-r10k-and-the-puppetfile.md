---
ID: 413
post_title: 'Puppet &#8211; r10k and the Puppetfile'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-r10k-and-the-puppetfile
published: true
post_date: 2015-06-10 00:00:00
---
The "Puppetfile" is list of all puppet modules you want downloaded into your puppet master's modules folder:


The first line of this file should be:


<pre>forge "http://forge.puppetlabs.com"</pre>


After that any puppet forge modules are listed as:


<pre>mod 'puppetlabs/stdlib',  '4.5.1'</pre>


Next any puppet modules hosted on git (e.g. github or any other git server, e.g. stash) can also be added, based on git's commit id:

<pre>
mod 'sudo',
  :git => 'https://github.com/saz/puppet-sudo.git',
  :ref => '231e15fb9311233ee0fe12f4d9bd6ec978d54a2c'
</pre>

or a particular, irrespective of branch:

<pre>
mod 'sudo',
  :git => 'https://github.com/saz/puppet-sudo.git',
  :ref => '231e15fb9311233ee0fe12f4d9bd6ec978d54a2c'
</pre>


or a particular branche's latest commit:

<pre>
mod 'sudo',
  :git => 'https://github.com/saz/puppet-sudo.git',
  :ref => '{branch-name}'
</pre>



Or even a particular git (version) tag:

<pre>
mod 'sudo',
  :git => 'https://github.com/saz/puppet-sudo.git',
  :tag => '1.2.0'
</pre>



Note, ensure you include all module dependencies. Then to check that you all dependencies are included, run:



<pre>$ puppet module list --tree --modulepath /etc/puppetlabs/code/environments/{env-name}/modules</pre>