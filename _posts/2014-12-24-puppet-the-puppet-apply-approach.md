---
ID: 241
post_title: 'Puppet &#8211; The &#8220;puppet apply&#8221; approach'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-the-puppet-apply-approach
published: true
post_date: 2014-12-24 00:00:00
---
Please ignore this post. and go to the next post. 

In the world of Puppet, classes are essentially functions. Once you have written your classes, they can then be loaded into memory (aka "define" the classes) and then triggered (aka "declared") from inside other manifests. These classes are then organized into <strong>modules</strong>, which we'll cover later.  

Going back to the previous example, lets now keep everything the same, except comment out the import key word:

<pre>
[root@puppetmaster manifests]# ls -l
total 12
-rw-r--r--. 1 root root 174 Dec 24 20:57 myclass.pp
-rw-r--r--. 1 root root 145 Dec 24 20:59 site.pp
[root@puppetmaster manifests]# mv myclass.pp myclasses.pp
[root@puppetmaster manifests]# ls -l
total 12
-rw-r--r--. 1 root root 174 Dec 24 20:57 myclasses.pp
-rw-r--r--. 1 root root 299 Dec 24 20:56 site24-12-14-v1.pp
-rw-r--r--. 1 root root 145 Dec 24 20:59 site.pp
[root@puppetmaster manifests]# cat myclasses.pp
class user_account ($username = 'homer'){

  user { $username:
    ensure => present,
    uid    => '101',
    shell  => '/bin/bash',
    home   => "/home/$username",
  }

}
[root@puppetmaster manifests]# cat site.pp
import 'myclass.pp'                          # note the use of the "import" keyword here

node 'PuppetAgent1' {
  class {user_account:}
}

node 'PuppetAgent2' {
  class {user_account:
    username => "bart",
  }
}
[root@puppetmaster manifests]#
</pre>

This time the puppetmaster will fail to generate the catalog when requested by an agent, because the puppetmaster has no idea where the user_account class is. However we can manually load it into Puppet's memory (aka define the class) by running the following command on the command line:

<pre>
[root@puppetmaster manifests]# puppet apply /etc/puppet/manifests/myclasses.pp
Notice: Compiled catalog for puppetmaster.codingbee.dyndns.org in environment production in 0.03 seconds
Notice: Finished catalog run in 0.03 seconds
[root@puppetmaster manifests]#
</pre>