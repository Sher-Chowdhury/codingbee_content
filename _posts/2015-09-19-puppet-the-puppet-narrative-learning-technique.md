---
ID: 447
post_title: 'Puppet &#8211; The Puppet Narrative (learning technique)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-the-puppet-narrative-learning-technique
published: true
post_date: 2015-09-19 00:00:00
---
The fastest way to learn Puppet is to get a full picture of the puppet infrastrucutre. The best way to do to this is if you have a story (aka narrative) to follow along that starts from the puppet basics to the advanced stuff. When you learn something new about puppet you can then link it back to the narrative. This will help you understand the bigger picture of using Puppet.  



<h2>Learning approach checklist</h2>

<ol>
	<li>Run a <a href="https://docs.puppetlabs.com/learning/ral.html#the-puppet-resource-command">Puppet resource command</a>, with a resource definition itself on the command line. Run this on all demo machines. E.g.:

<pre>
[root@puppetagent1 ~]# puppet resource file /tmp/testfile.txt ensure=present content="hello world"
Notice: /File[/tmp/testfile.txt]/ensure: created
file { '/tmp/testfile.txt':
  ensure  => 'file',
  content => '{md5}5eb63bbbe01eeed093cb22bb8f5acdc3',
}
[root@puppetagent1 ~]# cat /tmp/testfile.txt
hello world
[root@puppetagent1 ~]#

</pre>

This demonstrate how puppet is standardising how changes are implemented across all Linux distros. This solves: The need to keep up to date with the subtle command differences between each distros. New problem: tedious to run all this from the command line, and also no record of this. A better approach is write the resource definition into a puppet file (aka manifest).</li>

	<li>Copy earlier resource into a file called "MyFirstManifest.pp", then use the puppet apply command, on the master, and the agents". This solves: writing things in the command line, also lets your write lots of resources in a single file. New Problem: You'll have lots of manifests files spread across all your agents which will be difficult to keep track of. It would be much better if all your manifests are stored on one server, aka the puppetmaster</li>
	<li>Copy the code from MyFirstManifest.pp and place it into the master's site.pp file, then delete all existing testmanifest.pp. The site.pp file will look something like this:
<pre>
[root@puppetmaster tmp]# cat /etc/puppet/manifests/site.pp
node default {
  file { '/tmp/matestfile.txt':
    ensure => file,
    content => "hello world",
  }
}
[root@puppetmaster tmp]#

</pre>
Note, we used a special node definition called "default" . 

 and then do "puppet agent -t" from on all machines. This solves: All your puppet code is now on one machine, i.e. it is now in one place. What needs to improve: You may not want to apply the same resources to all agents. </li>
	<li>Cut and paste resource definitions into node definitions. This solves: resources are now targeting specific agents. New Problem: code duplication.</li>
	<li>remove duplicate code and convert code into class, then use the include statement. This solve: reduced code duplication. What needs to improve: classes are not versatile and quite static.</li>
	<li>Parameterise the class - Also replace the include-statements with resource-like class declaration.  This solves: classes are more versatile. New Problem: We could end up writing hundreds of class parameters, which makes class parameters quite complicated to declare</li>

	<li>Use Facter - This minimises the need of creating unnecessary class parameters. New Problem: We could end up writing hundreds of classes in the site.pp file, Hence need a way to organise these modules.</li>

	<li>Move the class into it's own init.pp manifest and create a module - This solve: moves classes out of site.pp and organise them into module, which ends up reducing the size of site.pp file. Also taken advantage of auto-loading. Existing Problem: We still have the problem that resource like class declarations still takes up a lot of lines in the site.pp. This is solved by utilising hiera</li>
	<li>Utilize Hiera - this will allow us to return back to using the includ-statements again in the site.pp. It also separates out the logic (that is stored in the puppet module) from the data (that is stored in the yaml files). New Problem: over time each node-definition could end up having a long list of class declarations, in the form of a series of "include" statements. It would be better if we could just have one include statement, this is possible by implementing a design approach called "Roles and Profiles"</li>
	<li>Implement Roles and Profiles - This doesn't introduce any new puppet feature, instead it introduces a way to organise all your puppet modules into 2 new modules called roles and profiles. This solves: now only have one include statement per node definition. Also all include statements align to business logic. The technical details, e.g. dbs, os security patches,....and etc, are hidden away in the profiles module. Things that could be improved: So far we have all data into yaml files. However these is still more data left in the site.pp, i.e. site.pp contains data about what nodenames are, and what classes are included in each node definitions.</li>
	<li>Implement ENC - this will result in a completely empty site.pp file. This is what we have been culminating too. Here we introduce Foreman.</li>

</ol>



The difference between a beginner puppet user, and an expert puppet user is that a beginner has a lots of lines of code in their site.pp file, and an expert user keeps their site.pp file completely empty.