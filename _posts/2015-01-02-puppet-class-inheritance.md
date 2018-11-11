---
ID: 254
post_title: 'Puppet &#8211; Class Inheritance'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-class-inheritance
published: true
post_date: 2015-01-02 00:00:00
---
Let's say want to create a module that let's you set up any of the following scenarios on each of your nodes:

<ul>
	<li>Creates a user (called 'homer') only</li>
	<li>Creates a user (called 'homer') and textfile. </li>
        <li>Creates a user (called 'homer') and ensures a service is running</li>
</ul>

Note here that the user called "homer" must always exist. 





There are 4 main ways to achieve this in your module's design:

<ol>
	<li>create 3 classes, one for each resource. In this scenario, you would have 3 manifests, e.g., init.pp (which contains the user_account class), ensure_file.pp, and ensure_service.pp.	</li>
	<li>have all resources in a single class in the init.pp file, and control weather file or service resource using class parameters.</li>
	<li>Write a class for each resource, assume the "user" resource is the main class. Also the file and service class also includes a user clss. </li>
	<li>use inheritance</li>
</ol>







Option 1 - this is not good, because it relies on the module's to remember to always call the user_account class before the ensure_file/ensure_service classes. This means that it makes it possible for user to only ensure textfile (by only including the user_account::esnure_file class), which is not how the module is supposed to be used, becuase it isn't one of the scenarios defined above. 

option 2 - Also not good because you have introduce extra class parameters along with if/else logic, which can be avoided. 

option 3 - Also not good, because it will result in code duplication, since the user resource has to be defined 3 times, once for each manifest.  

Option 4 - this is the best option. But only use <a href="https://docs.puppetlabs.com/puppet/latest/reference/lang_classes.fin">inheritance sparingly</a>. 

To achieve inheritance, we first have to start with implementing option 1. This means that we end up with:

<pre>
[root@puppetmaster modules]# tree user_account/
user_account/
└── manifests
    ├── ensure_file.pp
    ├── ensure_service.pp
    └── init.pp

[root@puppetmaster modules]# cd user_account/manifests/
[root@puppetmaster manifests]# cat init.pp 
class user_account {

  user { 'homer':
    ensure => 'present',
    uid => '121',
    shell => '/bin/bash',
    home => '/home/homer',
  }

}
[root@puppetmaster manifests]# cat ensure_file.pp 
class user_account::ensure_file {

  file {'/tmp/testfile.txt':
    ensure => file,
    content => "some important data. \n",
  }

}
[root@puppetmaster manifests]# cat ensure_service.pp 
class user_account {

  service {'sssd':
    ensure => running,
  }

}
[root@puppetmaster manifests]# 

</pre>

Note: here we used the "sssd" service just as an example. 

now our site.pp shows:

<pre>
[root@puppetmaster manifests]# cat site.pp 

node 'puppetmaster' {
  include user_account::ensure_file       # note, we don't reference the main class, only side one. 
}

[root@puppetmaster manifests]# 
</pre>

Now if we do a puppet run:

<pre>
[root@puppetagent1 tmp]# puppet agent --test
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Loading facts in /var/lib/puppet/lib/facter/licence.rb
Info: Caching catalog for puppetagent1.codingbee.dyndns.org
Info: Applying configuration version '1420274668'
Notice: /Stage[main]/User_account::Ensure_file/File[/tmp/testfile.txt]/ensure: defined content as '{md5}171ec60a7385ef54e21ed94eeab21850'
Notice: Finished catalog run in 0.04 seconds
[root@puppetagent1 tmp]# cat /etc/passwd | grep "homer"
[root@puppetagent1 tmp]# cat /tmp/testfile.txt
some important data.
[root@puppetagent1 tmp]#

</pre>

As you can see the user has not been created, but the file has been created. 

Now let's add the inheritance syntax to ensure_file.pp:

<pre>
[root@puppetmaster manifests]# cat ensure_file.pp
class user_account::ensure_file inherits user_account {

  file {'/tmp/testfile.txt':
    ensure => file,
    content => "some important data. \n",
  }

}

[root@puppetmaster manifests]#

</pre>

Notice the "inherits <fqdn of class name>" syntax. Now if we do the puppet run again:

<pre>
[root@puppetagent1 tmp]# cat /etc/passwd | grep "homer"
[root@puppetagent1 tmp]# cat /tmp/testfile.txt
cat: /tmp/testfile.txt: No such file or directory
[root@puppetagent1 tmp]# puppet agent --test
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Loading facts in /var/lib/puppet/lib/facter/licence.rb
Info: Caching catalog for puppetagent1.codingbee.dyndns.org
Info: Applying configuration version '1420275361'
Notice: /Stage[main]/User_account/User[homer]/ensure: created
Notice: /Stage[main]/User_account::Ensure_file/File[/tmp/testfile.txt]/ensure: defined content as '{md5}171ec60a7385ef54e21ed94eeab21850'
Notice: Finished catalog run in 0.11 seconds
[root@puppetagent1 tmp]# cat /etc/passwd | grep "homer"
homer:x:101:501::/home/homer:/bin/bash
[root@puppetagent1 tmp]# cat /tmp/testfile.txt
some important data.
[root@puppetagent1 tmp]#
</pre>

On this occasion, the module's sub class, ensure_file inherits the main class. You can think of inheritance, as a way to insert the entire "remote class" to the very beginning of the class in question. 

In real world situations, inheritance is used rarely. However one common way it is used is for all classes in a module inheriting a class that set's all the module parameter info. The <a href="https://github.com/puppetlabs/puppetlabs-ntp/tree/master/manifests">ntp puppet module</a> is a good example of this in action. 

In fact it is recommended to <a href="http://garylarizza.com/blog/2014/02/17/puppet-workflow-part-1/">only use class inheritance for inheriting params.pp</a>.  



<strong>See also:</strong>

https://docs.puppetlabs.com/puppet/latest/reference/lang_classes.html#inheritance

</pre>