---
ID: 243
post_title: 'Puppet &#8211; Modules, a quick taster'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-modules-a-quick-taster
published: true
post_date: 2014-12-24 00:00:00
---
We previously saw how to "dot-source" a manifest using the "import" keyword. Now this time, let's comment out the import keyword:

<pre>
[root@puppetmaster manifests]# pwd
/etc/puppet/manifests
[root@puppetmaster manifests]# ls -l
total 8
-rw-r--r--. 1 root root 173 Dec 24 23:07 myclasses.pp
-rw-r--r--. 1 root root 236 Dec 24 23:20 site.pp
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
# import 'myclasses.pp'                          # note the use of the "import" keyword here

node 'PuppetAgent1' {
  class {user_account:}
}

node 'PuppetAgent2' {
  class {user_account:
    username => "bart",
  }
}
</pre>

Now if we comment out the import keyword, and then do a manual puppet run, it fails:

<pre>
[root@puppetagent1 ~]# puppet agent --test
Info: Retrieving pluginfacts
Info: Retrieving plugin
Notice: /File[/var/lib/puppet/lib/puppet]/ensure: removed
Notice: /File[/var/lib/puppet/lib/facter]/ensure: removed
Error: Could not retrieve catalog from remote server: Error 400 on SERVER: Puppet::Parser::AST::Resource failed with error ArgumentError: Could not find declared class user_account at /etc/puppet/manifests/site.pp:10 on node puppetagent1.codingbee.dyndns.org
Warning: Not using cache on failed catalog
Error: Could not retrieve catalog; skipping run
[root@puppetagent1 ~]#
</pre> 

This has failed because the puppetmaster finds a reference to the user_account class is the site.pp, but has no idea where the pp file is where this class has been defined (since we have commented out the import statement). 

Now we'll perform a few tasks, just follow along for now and we'll explain what we have done later. Let's run the following command:

<pre>
[root@puppetmaster manifests]# puppet config print modulepath
/etc/puppet/modules:/usr/share/puppet/modules
[root@puppetmaster manifests]#
</pre>

Now do the following:
<ol>
	<li>cd into /etc/puppet/modules</li>
	<li>then create a folder called "user_account" (i.e. the folder name matches the above class's name)</li>
	<li>cd Into this new folder, then create a folder called "manifests"</li>
	<li>move the myclasses.pp file into this folder and also rename it to init.pp</li>
</ol>

Therefore:

<pre>
[root@puppetmaster manifests]# cd /etc/puppet/modules/
[root@puppetmaster modules]# ls
[root@puppetmaster modules]# mkdir user_account
[root@puppetmaster modules]# cd user_account/
[root@puppetmaster user_account]# mkdir manifests
[root@puppetmaster user_account]# cd manifests/
[root@puppetmaster manifests]# mv /etc/puppet/manifests/myclasses.pp init.pp
[root@puppetmaster manifests]# pwd
/etc/puppet/modules/user_account/manifests
[root@puppetmaster manifests]# ls
init.pp
[root@puppetmaster manifests]# cat init.pp
class user_account ($username = 'homer'){

  user { $username:
    ensure => absent,
    uid    => '101',
    shell  => '/bin/bash',
    home   => "/home/$username",
  }

}

</pre> 

Now if we do a manual run on an agent, this time it worked:

<pre>
[root@puppetagent1 ~]# puppet agent --test
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Caching catalog for puppetagent1.codingbee.dyndns.org
Info: Applying configuration version '1419480383'
Notice: /Stage[main]/User_account/User[homer]/ensure: created
Notice: Finished catalog run in 0.16 seconds
[root@puppetagent1 ~]# cat /etc/passwd | grep "homer"
homer:x:101:501::/home/homer:/bin/bash
[root@puppetagent1 ~]#
</pre> 

This time it has worked. That's because, when an agent submits a catalog request to the puppetmaster (which we did manually using "puppet apply --test"), the puppetmaster starts to determine what the catalog's content should be by first reading the content of the site.pp file. Here it encounters a reference to the user_account class. Since this class isn't defined in the site.pp file, the puppetmaster starts to look for it elsewhere, and by default it takes the following approach when looking for the class:

<ol>
	<li>First it finds what directory paths are listed in puppet's modulepath setting.</li>
	<li>It then looks inside this directory for a folder that has the same name as the class's name</li>
	<li>If it finds that, it then traverses into this directory and then looks for a folder called "manifests"</li>
	<li>If it finds that, it then traverses into this directory and then looks for a manifest called "init.pp"</li>
	<li>If it finds this manifests, and then looks into this file for the class it is looking for. </li>
	<li>If it finds the class, it then loads that class into memory and then repeats this process for any other class names in the site.pp.</li>
	<li>Once it has located all the classes that are referenced in the site.pp, it then compiles the catalog and sends it to the agent. 
</li>The agent reads the catalog and make any changes necessary to bring itself to the desired state. 	<li>

</ol>

As you can see, when the puppetmaster encounters a class name that it hasn't seen before, the puppetmasters default behaviour is to automatically look for the class's definition in specific manifest (called "init.pp"). But not just any init.pp file, this init.pp file must be in the following location:

<pre>
{modulepath}/{folder that's named after the class}/manifests/init.pp
</pre>

In puppet, the folder that's named after the class, and all it's content is referred to as a "module".

This behaviour of puppet makes it possible to organise all your resources into classes. And you can organise all your classes into modules (note, the rule is that init.pp has to contain one class definition, which is the name of the class that the puppet master is looking for). This will lead to the site.pp file to only contain node definitions which lists various classes.  
 
So far we have made a barebones module. We'll explore much more about modules as we progress this course. That's because modules are a core part of what makes puppet so powerful.