---
ID: 250
post_title: 'Puppet &#8211; Writing Custom Facts'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-writing-custom-facts
published: true
post_date: 2014-12-31 00:00:00
---
The custom facts rb files that we have seen so far ran shell commands on the agents. This obviously won't work for certain linux distros, or a windows agent. That's why custom facts need more code logic to be added so that it is compatible with other distros and windows machine. 

This can be done using an over-ride feature, in the form of the confine keyword, i think.

Also so far we have seen one line shell command's output being what we required. However sometime we need to write a multiline ruby/shell code block. In which case we can use the do...end block. 

Finally, you can create facts that actually contains an array or hash. These types of facts are referred to as <a href="https://docs.puppetlabs.com/facter/latest/fact_overview.html#writing-structured-facts">structured facts</a>.  




https://docs.puppetlabs.com/facter/latest/custom_facts.html
https://docs.puppetlabs.com/facter/latest/fact_overview.html


Once you have been added to an agent, you can then query them withe the -p option:

<pre>
$ facter -p | wc -l
132
$ facter | wc -l
99
</pre>

Here you can see that there are about 33 (132-99) custom facts available on top of the standard facts.