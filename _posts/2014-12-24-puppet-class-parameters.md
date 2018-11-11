---
ID: 239
post_title: 'Puppet &#8211; Class Parameters'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-class-parameters
published: true
post_date: 2014-12-24 00:00:00
---
In the previous lesson we had the following example:

<pre>
class homer_simpson {

  user { 'homer':
    ensure => present,
    uid    => '101',
    shell  => '/bin/bash',
    home   => '/home/homer',
  }

}

node 'PuppetAgent1' {
  class {homer_simpson:}
}

node 'PuppetAgent2' {
  class {homer_simpson:}
}
</pre>

Now what if we want the first agent to have the user, "homer", but the second agent to have the user, "bart". In that case we can't use the homer_simpson class anymore in it's current form. Instead we need to make this class more versatile by allowing it to accept parameters, aka class parameters. In this case we need to pass a parameter of "username", and also change the class's name to something more appropriate e.g. "user_account". This is how the code now looks:  

<pre>
[root@puppetmaster ~]# cat /etc/puppet/manifests/site.pp
class user_account ($username){

  user { $username:
    ensure => present,
    uid    => '101',
    shell  => '/bin/bash',
    home   => "/home/$username",
  }

}

node 'PuppetAgent1' {
  class {user_account:
    username => "homer",
  }
}

node 'PuppetAgent2' {
  class {user_account:
    username => "bart",
  }
}
</pre>

On agent1 this results in: 

<pre>
[root@puppetagent1 ~]# puppet agent --test
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Caching catalog for puppetagent1.codingbee.dyndns.org
Info: Applying configuration version '1419452655'
Notice: /Stage[main]/User_account/User[homer]/ensure: created
Notice: Finished catalog run in 0.15 seconds
[root@puppetagent1 ~]# cat /etc/passwd | grep "homer"
homer:x:101:501::/home/homer:/bin/bash
[root@puppetagent1 ~]#
</pre>

And on agent2 we have:
<pre>
[root@puppetagent2 ~]# puppet agent --test
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Caching catalog for puppetagent2.codingbee.dyndns.org
Info: Applying configuration version '1419452725'
Notice: /Stage[main]/User_account/User[bart]/ensure: created
Notice: Finished catalog run in 0.19 seconds
[root@puppetagent2 ~]# cat /etc/passwd | grep "bart"
bart:x:101:501::/home/bart:/bin/bash
[root@puppetagent2 ~]#

</pre>

We can also set a default value for a class parameter like this:


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

<h2>Variable Scope</h2>

In the above examples we have also introduced varables which are denoted by a "$" prefix. 

Any variables defined within a class are only accessible from within that class, if you use it's short-hand name. However you can still access it from outside the class by using it's full name:

<pre>${classname}::${variable_name}</pre>

We'll cover more about variables later. 

Here we used the double colon syntax to drill down to the correct location.