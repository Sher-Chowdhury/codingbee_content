---
ID: 359
post_title: 'Puppet &#8211; Setting user password'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-setting-user-password
published: true
post_date: 2015-04-15 00:00:00
---
When you use the user resource, you'll notice that it contains a password attribute. This can only be used to enter an encrypted password, aka a hashed password. For security reasons, you cannot use a plain text password. 

Also for some reason, you can't use the passwd command to create hash value and retrieve that value from teh /etc/shadow file either. However Puppet passes the password supplied in the user type definition into the /etc/shadow file.

You have to use the openssl command to generate the hash:

<pre> 
#openssl passwd -1  
#Enter your password here 
Password: 
Verifying - Password: 
 $1$HTumvYUGYUGwsxQxCp3F/nGc4DCYM
</pre>

You then insert this password into your resource:

<pre>user { 'TestUser': 
  ensure   => present,
  password => '$1$HTumvYUGYUGwsxQxCp3F/nGc4DCYM',
}</pre>