---
ID: 271
post_title: 'Puppet &#8211; Generate files from templates using Hiera data'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-generate-files-from-templates-using-hiera-data
published: true
post_date: 2015-01-19 00:00:00
---
Note: The approach I am about to cover is not best practice, for more info see: <a href="https://docs.puppet.com/hiera/latest/puppet.html#dont-use-the-lookup-functions-from-templates" target="_blank" rel="nofollow">https://docs.puppet.com/hiera/latest/puppet.html#dont-use-the-lookup-functions-from-templates</a> 



You can use hiera data to populate the placeholders in your template files. For 

For example if your class looks like this:

<pre>
# your class contains:
[root@puppetmaster user_account]# cat manifests/init.pp
class user_account ($username) {

  user { $username:
    ensure => present,
    shell  => '/bin/bash',
  }

  file {"/tmp/$username.txt":
    ensure => file,
    content => template("user_account/list-of-fruits.erb"),
  }

}

# your template contains:
[root@puppetmaster user_account]# cat templates/list-of-fruits.erb
<%-# Here is a simple ruby for-loop -%>
<%- for fruit in scope.function_hiera(["fruits"]) -%>
  A <%= fruit %> is a fruit.
<%- end -%>


# your global yaml file contains:
[root@puppetmaster user_account]# cat /etc/hieradata/yaml/global.yaml
---
user_account::username: homer

fruits:
  - pineapple
  - banana
  - carrot


</pre>

First notice that the fruits key holds an array. Also notice the hiera lookup syntax we used inside the erb file. When we want to call a function from inside an erb file, we have to always have to prefix the function name with "scope.function_" and the key has to be encased in double/single quotes followed by square brackets, and finally round brackets. 

When we then run this, we get:

<pre>
[root@puppetmaster user_account]# cat /tmp/homer.txt
cat: /tmp/homer.txt: No such file or directory
[root@puppetmaster user_account]# puppet agent -t
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Caching catalog for puppetmaster.codingbee.dyndns.org
Info: Applying configuration version '1421712095'
Notice: /Stage[main]/User_account/File[/tmp/homer.txt]/ensure: defined content as '{md5}0e2aaf2b34725bde9da190a23655960c'
Notice: Finished catalog run in 0.04 seconds
[root@puppetmaster user_account]# cat /tmp/homer.txt
  A pineapple is a fruit.
  A banana is a fruit.
  A carrot is a fruit.
[root@puppetmaster user_account]#
</pre>

However calling hiera lookups within erb files is not considered good practice. 

See also:
https://docs.puppetlabs.com/hiera/1/puppet.html#using-the-lookup-functions-from-templates