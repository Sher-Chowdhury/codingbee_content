---
ID: 255
post_title: 'Puppet &#8211; What is Hiera'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-what-is-hiera
published: true
post_date: 2015-01-02 00:00:00
---
Hiera is a powerful way to store (class parameter) data outside of your pp files. Hiera also stores this data in a efficient hierarchial structure so to minimize code duplication. This is especially useful when you want to declare a class that requires a lot of class parameters.  

For example, Let's say our user_account's module's class requires 5 class parameters. Then if we declare this class across all our nodes, then it will look something like this:



<pre>
[root@puppetmaster ~]# cat /etc/puppet/manifests/site.pp

node 'PuppetAgent1' {
  class {user_account:
    username => "bart",
    password => "Liverpool",
    password_max_age => "90",
    password_min_age => "10",
    shell => /bin/bash,
  }
}

node 'PuppetAgent2' {
  class {user_account:
    username => "bart",
    password => "Liverpool",
    password_max_age => "90",
    password_min_age => "10",
    shell => /bin/bash,
  }
}

node 'PuppetAgent3' {
  class {user_account:
    username => "bart",
    password => "Liverpool",
    password_max_age => "90",
    password_min_age => "10",
    shell => /bin/bash,
  }
}

</pre>

As you can see we are passing the same parameter values for these nodes. There's 2 problems with this. First, if for each node, we need to change the passord parameter from "Liverpool" to another value, e.g. "Manchester", then that's going to become tedious. Secondly declaring class's using the class-syntax takes up a lot of lines of code. 

However thanks to Hiera, we can seperate out the class parameter data so that our manifest now looks like this:

<pre>  
node 'PuppetAgent1' {
  include user_account
}

node 'PuppetAgent2' {
  include user_account
}

node 'PuppetAgent3' {
  include user_account
}

node 'PuppetAgent4' {
  include user_account
}

</pre>

This is possible, thanks to hiera. With hiera, all the data is stored in a bunch of yaml files, and then Puppet requests for the data through hiera, which in turn queries the yaml files. Hiera queries the yaml files in "fallback style". For example you may have heard of the saying:



<blockquote>
You always need a plan B, just in case plan A doesn't work. 
</blockquote>

Hiera (along with Puppet) takes this concept to the next level, and has plan c, plan d, plan e....and etc. In the context of puppet+hiera, these plans are to do with locating the data, when classes are declared using the "include" statement.  

<h2>The Hiera lookup order</h2>
So when puppet encounters an include statement, but the corresponding class requires class parameters, then the following happens:
<ol>
	<li>puppetmaster asks hiera to find the info. </li>
	<li>Hiera then asks puppetmaster for the agent's facter data and puppet builtin variables. These are available within the manifests node-defintion.</li>
	<li>hiera then identifies a list of yaml files that it needs to look through to find the data. This is done through hiera hierarchy. We will cover more about this later.</li>
	<li>Hiera then checks if the first file exists (plan A), if it doesn't exist, or file exist, but file exists but doesn't have the info, then hiera moves on to the next file, aka plan B.</li>
	<li>It repeats the previous process with second file (plan B). And if it fails again, then moves on to the next file, aka plan C. This cycle continues until the data is found, or hiera has run out of yaml files.</li>
	<li>if hiera fails to find the data, then it will notify puppetmaster of this by returning "nil"</li>
	<li>puppetmaster will then, as a last resort, see if the class has any default parameters defined. If it does, then it will use them instead.</li>
	<li>If no default values are defined in the class definitions, then the current puppet run will terminate and an error will be displayed, since the puppetmaster was unable to compile the catalog.</li>
 

</ol>

If we reach the last step then it means we need to investigate and fix the issue. 

 




See also:
https://docs.puppetlabs.com/hiera/1/index.html