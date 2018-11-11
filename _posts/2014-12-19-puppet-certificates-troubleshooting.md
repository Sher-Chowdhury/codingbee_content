---
ID: 228
post_title: 'Puppet &#8211; Certificates troubleshooting'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-certificates-troubleshooting
published: true
post_date: 2014-12-19 00:00:00
---
useful link:  
https://docs.puppetlabs.com/puppet/3.7/reference/ssl_regenerate_certificates.html 
http://gerrit.googlecode.com/svn/documentation/2.2.1/pgm-init.html



If you get the following message:

<pre>
Error: Could not request certificate: SSL_connect returned=1 errno=0 state=SSLv3 read server certificate B: certificate verify failed:
</pre>


When you get this error message, try the following:

1. locate the puppetmaster's ssl file:

<pre>
puppet config print ssldir
</pre>


2. Delete the ssl directory:

<pre>
rm -rf ssl/
</pre>

3. Restart the puppetmaster service:

<pre>
service puppetmaster restart
</pre>


4. On the puppet agent, then run:

<pre>
puppet agent -t --trace -debug
</pre>


This should work now. 

The reason this problem occured is because the agent tried to contact a master with a particular ssl. It is bit like the puppetmaster signing the certificate with the wrong signature to the one the master is expecting from the agent requests. 


See also:

https://docs.puppetlabs.com/background/ssl/