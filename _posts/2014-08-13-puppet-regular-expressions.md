---
ID: 140
post_title: 'Puppet &#8211; Regular expressions'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-regular-expressions
published: true
post_date: 2014-08-13 00:00:00
---
https://docs.puppetlabs.com/puppet/latest/reference/lang_datatypes.html#regular-expressions

&nbsp;

&nbsp;

In bash you encase regular expressions in double-quotes. However in puppet you encase them in forward slashes. I.e. this is wrong:

&nbsp;

"regex"

&nbsp;

but this is correct:

&nbsp;

/regex/

This is the <a href="http://en.wikipedia.org/wiki/Regular_expression#Syntax">perl syntax of regex</a>