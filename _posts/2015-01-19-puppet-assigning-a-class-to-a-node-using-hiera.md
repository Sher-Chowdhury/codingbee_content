---
ID: 270
post_title: 'Puppet &#8211; Assigning a class to a node using Hiera'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-assigning-a-class-to-a-node-using-hiera
published: true
post_date: 2015-01-19 00:00:00
---
Once you have moved all your class parameter definitions out of site.pp and into hiera, all that will left will be your node definitions, and these node definitions will just contain a list of include statements for various modules.


All the the other data will mostly be stored in your various yaml files which are most likely named after your agent's hostname, e.g. hostname.yaml. 

However for each hostname.yaml file you can add in an array which lists all your classes, e.g.:

<pre>
[root@puppetmaster puppet]# cat /etc/puppet/hieradata/yaml/puppetagent1.yaml
---

classes:
  - user_account

user_account::username: Tom
[root@puppetmaster puppet]#
</pre>

 
After that in your site.pp file, you can use the hiera_include function which will redirect the puppetmaster to seek the list of classes (to be included) from hiera. Therefore in our site.pp file we will now end up with just the following:

<pre>
[root@puppetmaster puppet]# cat /etc/puppet/manifests/site.pp
node default {
  hiera_include('classes')
}
[root@puppetmaster puppet]#
</pre>

In this example we chose the lookup key to be "classes", which is best practice, but not mandatory and can be changed to something else. 


Now if we do a puppet run: 
 
<pre>
[root@puppetagent1 /]# puppet agent -t
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Caching catalog for puppetagent1.codingbee.dyndns.org
Info: Applying configuration version '1424555958'
Notice: /Stage[main]/User_account/User[Tom]/ensure: created
Notice: /Stage[main]/User_account/File[/tmp/Tom.txt]/ensure: created
Notice: Finished catalog run in 0.27 seconds
[root@puppetagent1 /]# cat /etc/passwd | grep "Tom"
Tom:x:505:506::/home/Tom:/bin/bash
[root@puppetagent1 /]# ls -l /tmp/Tom.txt
-rw-r--r--. 1 root root 0 Feb 21 21:59 /tmp/Tom.txt
[root@puppetagent1 /]# puppet agent -t
</pre>


The benefit of this approach is that we have turn into hiera into a form of an External Node Classifier (ENC) via the use of the hiera_include function. However there are a lot of alternative ENCs which we'll cover next. 




See also:

 
https://docs.puppetlabs.com/hiera/1/puppet.html#best-practices

https://docs.puppetlabs.com/hiera/1/complete_example.html#assigning-a-class-to-a-node-with-hiera