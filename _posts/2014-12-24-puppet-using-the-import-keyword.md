---
ID: 242
post_title: 'Puppet &#8211; Using the &#8220;import&#8221; keyword'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-using-the-import-keyword
published: true
post_date: 2014-12-24 00:00:00
---
The "import" key word is actually going to be depecrated in future releases of Puppet, and that's because using modules are a much more powerful alternative. 

However it is still worth learning about "import" so to understand the origins of modules.

Previously we had:

<pre>
[root@puppetmaster ~]# cat /etc/puppet/manifests/site.pp
class user_account ($username = 'homer'){

  user { $username:
    ensure => present,
    uid    => '101',
    shell  => '/bin/bash',
    home   => "/home/$username",
  }

}

node 'PuppetAgent1' {
  class {user_account:}
}

node 'PuppetAgent2' {
  class {user_account:
    username => "bart",
  }
}
[root@puppetmaster ~]#

</pre>

The next logical step is to separate out the class definitions and place them into a separate manifest. In this case the manifest will reside in the same folder as the site.pp, e.g.:

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

We have pretty much dot-sourced the myclasses.pp in the site.pp file. 

The above gives the same results as having all the code in the site.pp file. 


<strong>
See also</strong>
https://docs.puppetlabs.com/puppet/latest/reference/lang_import.html