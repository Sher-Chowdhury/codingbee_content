---
ID: 256
post_title: 'Puppet &#8211; Adding a class to a node (aka declaring a class)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-adding-a-class-to-a-node-aka-declaring-a-class
published: true
post_date: 2015-01-03 00:00:00
---
Let's say we have:

<pre>
[root@puppetmaster modules]# tree user_account/
user_account/
├── files
├── lib
├── manifests
│     └── init.pp
├── spec
└── templates

5 directories, 1 file
[root@puppetmaster modules]# cat user_account/manifests/init.pp
class user_account ($username = 'homer'){

  user { $username:
    ensure => present,
    uid    => '101',
    shell  => '/bin/bash',
    home   => "/home/$username",
  }

}
[root@puppetmaster modules]#
</pre>

So far, we have seen 2 ways for declaring a class (aka adding a class to a node). 


The first way is to use the include-like syntax:

<pre>
node 'PuppetAgent1' {
  include user_account
}

</pre>


However there is a limitation with this, which is that the <code>include</code> syntax doesn't let you specify any class parameters. This means that you have to stick with the class parameter's default value. 



The second way is to use the class-like syntax:
<pre>
node 'PuppetAgent1' {
  class {user_account:}                  # this will create a user called "homer"
}

</pre>

The class-like syntax appears to be a better alternative to the include statement, because it let's you set class parameters:


<pre>
node 'PuppetAgent1' {
  class {user_account:
    username => "bart",
  }
}

</pre>

However the class-like syntax however has it's own limitations, which is that you can't declared the class more than once, even with different class-parameters, e.g. if you have the following in your site.pp file:

<pre>
[root@puppetmaster modules]# cat /etc/puppet/manifests/site.pp
node 'PuppetAgent1' {

  class {user_account:}

  class {user_account:
    username => "bart",
  }

  class {user_account:
    username => "lisa",
  }

}

node 'PuppetAgent2' {
  class {user_account:
    username => "bart",
  }
}
[root@puppetmaster modules]#

</pre>

Here the intention is to create 3 users, homer, bart, and lisa.


However when we do a puppet run, we get:


<pre>
[root@puppetagent1 tmp]# puppet agent --test
Info: Retrieving pluginfacts
Info: Retrieving plugin
Error: Could not retrieve catalog from remote server: Error 400 on SERVER: Duplicate declaration: Class[User_account] is already declared in file /etc/puppet/manifests/site.pp:3; cannot redeclare at /etc/puppet/manifests/site.pp:7 on node puppetagent1.codingbee.dyndns.org
Warning: Not using cache on failed catalog
Error: Could not retrieve catalog; skipping run
[root@puppetagent1 tmp]#
</pre>

Note: you also can't use an include hack like this to get around this:

<pre>
[root@puppetmaster modules]# cat /etc/puppet/manifests/site.pp
node 'PuppetAgent1' {

  include user_account
  $username = "bart"
  include user_account
  #$username = "lisa"
  #include user_account
}

node 'PuppetAgent2' {
  class {user_account:
    username => "bart",
  }
}
[root@puppetmaster modules]#

</pre>

That's becuase the class will use the class parameter's default value, rather than looking up higher scopes. Also you you can't <a href="https://docs.puppetlabs.com/puppet/latest/reference/lang_variables.html#no-reassignment">reassign variable within the same scope</a>. Hence will fail when trying reassign $username to the value "Lisa"


There are a couple of solutions to this, first, you can rewrite your class into very own customised resource type, which in puppet is referred to as a "defined type". We'll cover how to write defined types in the next article. 

There is another option, which is achieved using <a href="https://docs.puppetlabs.com/learning/modules2.html#why-include-cant-directly-take-class-parameters">Hiera</a>. We'll cover Hiera later in the course. 




<strong>See also</strong>
https://docs.puppetlabs.com/puppet/latest/reference/lang_classes.html#declaring-classes