---
ID: 487
post_title: 'spacewalk &#8211; notes'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/uncategorized/spacewalk-notes
published: true
post_date: 2015-11-19 00:00:00
---
deregister vm from spacewalk:

rm /etc/sysconfig/rhn/systemid

<h2>Upload rpm to spacewalk</h2>

<pre>$ Rpm --addsign name.rpm
$ Rhnpush -c {channel's-label} --server localhost name.rpm</pre>
 
https://fedorahosted.org/spacewalk/wiki/UploadFedoraContent