---
ID: 309
post_title: 'Puppet &#8211; The Augeas resource type'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-the-augeas-resource-type
published: true
post_date: 2015-03-02 00:00:00
---
In the previous demo, we saw how to makd a change via the command line. We used the "/etc/hosts" file as the example. We'll now show you how to make the same change, but this time with the <a href="https://docs.puppetlabs.com/references/latest/type.html#augeas">augeas puppet resource</a>. 


You can therefore do the same thing as via the command line using the following resource:

<pre>
  augeas{"hostfile" :
    context    => "/files/etc/hosts/3/",
    changes  => "set ipaddr 8.8.8.8",
  }
</pre>


If you want to append to a file, then you need to create a new node. You can achieve this using the <a href="https://github.com/hercules-team/augeas/wiki/Adding-nodes-to-the-tree">last()</a> function. 
 

<h3>
the "/etc/sudoers" file</h3>
Let's say you want to add the following line to your /etc/sudoers file:

<pre>
Defaults:tom !requiretty
tom ALL = (ALL) ALL
</pre>

This can be achieved using the following resource:

<pre>
  augeas { "sudotom":
    context => "/files/etc/sudoers",
    changes => [
      "set Defaults[type=':tom']/type :tom",
      "set Defaults[type=':tom']/requiretty/negate ''",
      "set spec[user = 'tom']/user tom",
      "set spec[user = 'tom']/host_group/host ALL",
      "set spec[user = 'tom']/host_group/command ALL",
      "set spec[user = 'tom']/host_group/command/runas_user ALL",
    ],
  }
</pre> 






See also:
https://docs.puppetlabs.com/guides/augeas.html