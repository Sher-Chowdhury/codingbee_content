---
ID: 415
post_title: 'Puppet &#8211; Ordering your classes'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-ordering-your-classes
published: true
post_date: 2015-06-10 00:00:00
---
this is common, for example, sometimes once class installs dbg software, and then the next class creates the db istself.


<pre>
class mainclass {
  include class1
  include class2
  include class3
  include class4

  Class['class1']
  -> Class['class2']
  -> Class['class3']
  -> Class['class4']
} </pre>