---
ID: 106
post_title: 'Puppet &#8211; Classes'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-classes
published: true
post_date: 2014-05-28 00:00:00
---
In Puppet we use "classes" to reduce code repetition. 

For example lets say we have:


<pre>
[root@puppetmaster manifests]# cat site.pp

node 'PuppetAgent1' {

  user { 'homer':
    ensure => present,
    uid    => '101',
    shell  => '/bin/bash',
    home   => '/home/homer',
  }
}

node 'PuppetAgent2' {

  user { 'homer':
    ensure => present,
    uid    => '101',
    shell  => '/bin/bash',
    home   => '/home/homer',
  }
}

</pre>

Here we have 2 agents where the the same user accounts needs to exist called "homer". As you can see we repeated the same resource twice. One way to avoid this is by combining the 2 nodes, like this:

<pre>
[root@puppetmaster manifests]# cat site.pp

node 'PuppetAgent1','PuppetAgent2' {

  user { 'homer':
    ensure => present,
    uid    => '101',
    shell  => '/bin/bash',
    home   => '/home/homer',
  }
}

</pre>

However merging nodes like this isn't the best option because it's likely that one agent needs a different combination of resources than another, while only sharing the odd few resources. 

Hence a better option is rewriting the resource into a class, using the class-syntax, and then adding the class to each node using the "include" keyword, i.e.

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
  include homer_simpson
}

node 'PuppetAgent2' {
  include homer_simpson
}

</pre>

Note: the class's names must always be in lower-case only. Hence something like "Homer_Simpson" won't work. 


In the above example we used the "<a href="https://docs.puppetlabs.com/puppet/latest/reference/lang_classes.html#declaring-classes">include-like</a>" syntax to add the class to a node definition (aka declare a class). There is another way to declare a class, which is by using the <a href="https://docs.puppetlabs.com/puppet/latest/reference/lang_classes.html#resource-like-behavior">resource-like</a> syntax: 

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

Notice how the class syntax basically looks like you are defining a new resource of the type "class". At first sight both syntax appears to do the same thing, however as you progress through this course you'll discover that each syntax has it's own features. Hence the syntax you pic depends on the circumstance. For instance the resource-like syntax let's you pass class parameters to the class (which we'll cover in the next tutorial), whereas the include-like syntax can also do this but through a utility called Hiera. 


See also:
https://docs.puppetlabs.com/puppet/latest/reference/lang_classes.fin