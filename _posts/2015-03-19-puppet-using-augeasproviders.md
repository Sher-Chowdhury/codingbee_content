---
ID: 327
post_title: 'Puppet &#8211; Using Augeasproviders'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-using-augeasproviders
published: true
post_date: 2015-03-19 00:00:00
---
While the augeas resource type is really powerful, it is relatively complicated to craft an augeas resource types. There are a few puppet modules that run on top of augeas and simplifies the use of augeas by providing custom resource types for editing specific config files:


<a href="http://augeasproviders.com/documentation/examples.html">http://augeasproviders.com/documentation/examples.html</a>

I think the shellvar module is for editing any variable=value type config files.