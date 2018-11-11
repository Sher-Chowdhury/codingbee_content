---
ID: 2809
post_title: 'Apache &#8211; Controlling access using the Order, Deny, and Allow directives on CentoS/RHEL 7'
author: sher
post_excerpt: >
  In this article are going to walkthrough
  the high-level Apache configuration
  settings.
layout: post
permalink: >
  https://codingbee.net/tutorials/open-source-software/apache-web-server/apache-controlling-access-using-the-order-deny-and-allow-directives
published: true
post_date: 2018-03-05 11:45:20
---
Apache let's you Allow/Deny access based on who is requesting access using the <a href="https://httpd.apache.org/docs/2.4/mod/mod_access_compat.html#order" target="_blank" rel="nofollow">Order</a> directive which is used in conjunction with the <a href="https://httpd.apache.org/docs/2.4/mod/mod_access_compat.html#allow" target="_blank" rel="nofollow">Allow</a> and <a href="https://httpd.apache.org/docs/2.4/mod/mod_access_compat.html#deny" target="_blank" rel="nofollow">Deny</a> directives. 


You can sepcify multiple Allow and Deny rules like this:

<pre>
Deny from {ip address}
Allow from {192.168.10.0/24}
Deny from dodgydomain.com


Order deny,allow
</pre>