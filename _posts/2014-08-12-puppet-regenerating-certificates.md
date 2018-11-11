---
ID: 137
post_title: 'Puppet &#8211; Regenerating certificates'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-regenerating-certificates
published: true
post_date: 2014-08-12 00:00:00
---
https://docs.puppetlabs.com/puppet/latest/reference/ssl_regenerate_certificates.html
on agent do:

<pre>
puppet config print ssldir   # this should output something like:
</pre>

We'll assume for the rest of this article that the above outputs:

<pre>
/var/lib/puppet/ssl 
</pre>

In this directory, go view what files it has:


<pre>

[root@puppetmaster ssl]# find . -name puppetmaster*
./public_keys/puppetmaster.*.pem
./certificate_requests/puppetmaster.*.pem
./certs/puppetmaster.*.pem
./private_keys/puppetmaster.*.pem
./ca/signed/puppetmaster.*.pem
[root@puppetmaster ssl]# 
</pre>

Run:

<pre>
puppet cert clean {agent's fqdn}         # note the "-all" option doesn't work. 
</pre>

this should then result in:

<pre>
[root@puppetmaster ssl]# find . -name puppetmaster*
[root@puppetmaster ssl]# 

</pre>

As you can see, nothing found this time. 

Now on the agent do:

<pre>
puppet agent -t --trace --debug
</pre>

This command will outpupwdt a message like:

<pre>
Exiting; no certificate found and waitforcert is disabled
</pre>

Now on the puppetmaster, you should the following files have been regenerated:

<pre>
[root@puppetmaster ssl]# pwd
/var/lib/puppet/ssl
[root@puppetmaster ssl]# find . -name puppetmaster*
./public_keys/puppetmaster.ordsvy.gov.uk.pem
./certificate_requests/puppetmaster.fqdn.pem
./private_keys/puppetmaster.fqdn.pem
./ca/requests/puppetmaster.fqdn.pem
[root@puppetmaster ssl]# 

</pre>

Now ensure that we sign. 

To sign this certificate do:

<pre>
puppet cert sign {agent's fqdn} 
</pre>


This should now result in:


<pre>
[root@puppetmaster ssl]# pwd
/var/lib/puppet/ssl
[root@puppetmaster ssl]# find . -name puppetmaster*
./public_keys/puppetmaster.ordsvy.gov.uk.pem
./certificate_requests/puppetmaster.ordsvy.gov.uk.pem
./private_keys/puppetmaster.ordsvy.gov.uk.pem
./ca/signed/puppetmaster.ordsvy.gov.uk.pem
[root@puppetmaster ssl]# 

</pre>



https://docs.puppetlabs.com/references/3.5.1/man/cert.html




on master do:

<pre>puppet cert clean {agent's fqdn}</pre>

then you can redo <a href="http://codingbee.net/tutorials/puppet/puppet-crash-course-setting-masteragent-relationship/" title="Puppet - Install/set-up the Puppet Master and it’s Agents">part c</a>