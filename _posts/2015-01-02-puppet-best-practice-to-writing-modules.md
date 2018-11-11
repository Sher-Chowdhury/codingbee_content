---
ID: 252
post_title: 'Puppet &#8211; Best practice to writing modules'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-best-practice-to-writing-modules
published: true
post_date: 2015-01-02 00:00:00
---
the ntp module is an example of a really well written module. 


https://docs.puppetlabs.com/guides/module_guides/bgtm.html



any yum related activicivities should reside  in it's own manifest called: <code>install.pp</code>
any config file related activities should reside in it's own manifest called: <code>config.pp</code>
any service start/stop related activities should reside in it's own manifest called: <code>service.pp</code>


These are best practice, although I don't think they are mandatory. 


All class parameters should have default values, so that people can use the module just be calling "include class_name". All the default parameter values should be defined in it's own manifest, which we should call <code>params.pp</code>.