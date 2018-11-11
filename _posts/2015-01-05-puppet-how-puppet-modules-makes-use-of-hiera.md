---
ID: 259
post_title: 'Puppet &#8211; How Puppet Modules makes use of Hiera'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-how-puppet-modules-makes-use-of-hiera
published: true
post_date: 2015-01-05 00:00:00
---
<h3>Modules making use of simple string data that has been retrieved from Hiera</h3>
There are 2 syntax styles that you can use to declare a class, <a href="https://docs.puppetlabs.com/puppet/latest/reference/lang_classes.html#include-like-vs-resource-like">include-like, and resource-like</a>. Hence we have:

<pre>
include user_account

# or 

class {'user_account':}
</pre>


During a puppet run, whenever a puppet master encounters a resource declaration for a class, then the puppetmaster will do the following:

<ol>
	<li>checks to see if the class has any class parameters.</li>
	<li>If there are class parameters, If resource-like declaration is used, then check if any class parameters have been defined. </li>
	<li>if no luck, then it requests hiera to find the information</li>
	<li>If hiera returns nil, then it will resort to using the class parameter's default values, if any has been defined in the class definition</li>
	<li>If no defaults have been specified then the puppet master fails and returns an error</li>
</ol>




Let's assume for now that we haven't setup/configured hiera yet. Also let's say that in our site.pp we have:
<pre>
node 'puppetmaster' {
  include user_account
}

</pre>
It's impossible to tell from this whether we need to specify any class parameters for the user_account class, and if so, whether there are any default values pre-defined as part of the class definition.

Hence we need to take a look at the class definition, which is in the module's init.pp file:

<pre>
[root@puppetmaster /]# tree /etc/puppet/modules/user_account/
/etc/puppet/modules/user_account/
├── files
├── lib
├── manifests
│     └── init.pp
├── spec
└── templates

5 directories, 1 file

[root@puppetmaster /]# cat /etc/puppet/modules/user_account/manifests/init.pp
class user_account ($username) {

  user { $username:
    ensure => present,
    shell  => '/bin/bash',
    home   => "/home/$username",
  }

  file {"/tmp/$username.txt":
    ensure => file,
    content => "This machine is called $::hostname \n",     #notice, that we used a facter value here.
  }

}
</pre>
Here we can see that, we do have a class parameter, "$username", which needs to be defined.  However the class doesn't specify a default value for this class parameter. That means that if we do a puppet run as is, then the puppet master won't be able to resolve the class variable and will then fail, like this:
<pre>[root@puppetmaster /]# puppet agent -t
Info: Retrieving pluginfacts
Info: Retrieving plugin
<span style="color: #ff0000;">Error: Could not retrieve catalog from remote server: </span>
<span style="color: #ff0000;">Error 400 on SERVER: Must pass username to Class[User_account] at /etc/puppet/manifests/site.pp:2 on node puppetmaster.codingbee.dyndns.org
Warning: Not using cache on failed catalog
Error: Could not retrieve catalog; skipping run
</span>[root@puppetmaster /]#
</pre>

To fix this, we can define the class parameter in the site.pp file, by using the resource-like class declaration syntax. However that will end up adding a lot more lines of code in our site.pp file. Hence we want to continue to use the "include" syntax instead of the resource-like class syntax. 


Otherwise the puppetmaster by default will automatically ask Hiera for this information, aka <a href="https://docs.puppetlabs.com/hiera/1/puppet.html#automatic-parameter-lookup">automatic parameter lookup</a>. If this also fails, then the puppetmaster resorts to using the default parameter values, if any have been defined in the class definition. If not, then the Puppetmaster throws an error. 

When puppetmaster submits a request to hiera, it provides the parameter's key in the form a of an fqdn. This key hence takes the form of:

<pre>
{class-name}::{parameter-name}
</pre>

Note, if the class parameter is in a sub-class, then we do:

<pre>
{module-name}::{class-name}::{parameter-name}
</pre>
 


The puppetmaster also always tell's hiera the path to the hiera.yaml file it should use, in the form of <a href="https://docs.puppetlabs.com/references/latest/configuration.html#hieraconfig">hiera_config</a> puppet.conf variable, which by default is set to /etc/puppet/hiera.yaml. It does this via hiera's "--config" option:


<pre>
Usage: hiera [options] key [default value] [variable='text'...]

The default value will be used if no value is found for the key. Scope variables
will be interpolated into %{variable} placeholders in the hierarchy and in
returned values.

    -V, --version                    Version information
    -d, --debug                      Show debugging information
    -a, --array                      Return all values as an array
    -h, --hash                       Return all values as a hash
    <strong>-c, --config CONFIG              Configuration file</strong>
    -j, --json SCOPE                 JSON format file to load scope from
    -y, --yaml SCOPE                 YAML format file to load scope from
    -m, --mcollective IDENTITY       Use facts from a node (via mcollective) as scope
    -i, --inventory_service IDENTITY Use facts from a node (via Puppet's inventory service) as scope

</pre> 
  
Therefore, when the puppetmaster automatically submit's a lookup request to hiera (as in the case above), then behind the scenes puppet runs the following hiera command:

<pre>

hiera user_account::username --config /etc/puppet/hiera.yaml

</pre>

This means our yaml file needs to contain a key called "user_account::username". For Hiera, the double colons are treated like any other literal characters, and hence doesn't have any special meaning to hiera. So let's say, our hiera.yaml file points to just one yaml file, which is /etc/hieradata/yaml/global.yaml, and this file contains:

<pre>
---
user_account::username: homer 

</pre>
  
In that case we would end up with:

<pre>
[root@puppetmaster /]# hiera user_account::username --config /etc/puppet/hiera.yaml
homer

</pre>

So if our site.pp file contains:
<pre>
[root@puppetmaster puppet]# cat manifests/site.pp
node 'puppetmaster' {
 class {user_account:}
}
</pre>

Note here that we didn't specify any parameter values, so this will prompt puppetmaster to try a hiera lookup. The same would have happened if we used the include-syntax. 

Then we would get the following during an agent run:

<pre>
[root@puppetmaster puppet]# cat /etc/passwd | grep "homer"
[root@puppetmaster puppet]# puppet agent -t
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Caching catalog for puppetmaster.codingbee.dyndns.org
Info: Applying configuration version '1421705732'
Notice: /Stage[main]/User_account/User[homer]/ensure: created
Notice: Finished catalog run in 0.34 seconds
[root@puppetmaster puppet]# cat /etc/passwd | grep "homer"
homer:x:501:504::/home/homer:/bin/bash
[root@puppetmaster puppet]#
</pre>

Success!


<h3>Modules making use of arrays that has been retrieved from Hiera</h3>

Earlier we saw that yaml files can be used to store mapped sequences (aka arrays). In most programming languages, arrays are iterated through loops. However in puppet there is no such thing as "loops" in manifest. However you can pass an array straight into a resource's title.  

For example, lets say our class shows:

<pre>
[root@puppetmaster modules]# tree user_account/
user_account/
├── files
├── lib
├── manifests
│     └── init.pp
├── spec
└── templates
    └── list-of-fruits.erb

5 directories, 2 files
[root@puppetmaster modules]# cat user_account/manifests/init.pp
class user_account ($username) {

  user { $username:
    ensure => present,
    shell  => '/bin/bash',
  }

}

[root@puppetmaster modules]# cat /etc/puppet/manifests/site.pp
node 'puppetmaster' {
 class {user_account:}
}

</pre>

So far nothing special. However the hiera.yaml file points to the following (and only) yaml file:

<pre>
[root@puppetmaster modules]# cat /etc/hieradata/yaml/global.yaml
---

user_account::username:
  - homer
  - marge
  - bart
  - lisa
  - maggie

fruits:
  - apple
  - banana
  - carrot
[root@puppetmaster modules]#

</pre>

As you can see, the user_account::username key is actually storing a array. Now if we run this, we get:

<pre>
[root@puppetmaster user_account]# cat /etc/passwd | grep -E 'homer|marge|bart|lisa|maggie'
[root@puppetmaster user_account]# puppet agent -t
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Caching catalog for puppetmaster.codingbee.dyndns.org
Info: Applying configuration version '1421710886'
Notice: /Stage[main]/User_account/User[marge]/ensure: created
Notice: /Stage[main]/User_account/User[lisa]/ensure: created
Notice: /Stage[main]/User_account/User[homer]/ensure: created
Notice: /Stage[main]/User_account/User[maggie]/ensure: created
Notice: /Stage[main]/User_account/User[bart]/ensure: created
Notice: Finished catalog run in 0.43 seconds
[root@puppetmaster user_account]# cat /etc/passwd | grep -E 'homer|marge|bart|lisa|maggie'
marge:x:503:506::/home/marge:/bin/bash
lisa:x:504:504::/home/lisa:/bin/bash
homer:x:505:507::/home/homer:/bin/bash
maggie:x:506:508::/home/maggie:/bin/bash
bart:x:507:509::/home/bart:/bin/bash
[root@puppetmaster user_account]#
</pre> 


That is how arrays are used in manifests. In the above example we created multiple user account, but you can also do the same think with any resource types, e.g. package, service, file....etc. 
















See also:

http://www.craigdunn.org/2011/10/puppet-configuration-variables-and-hiera/

This makes reference to:

class myapplication ( $webname = hiera("webname") ) {
...
}

Which is a handy way to force a class to look up the data from hiera. In my case, I should use:

$username = hiera("user_account::username")