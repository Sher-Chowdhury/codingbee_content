---
ID: 264
post_title: 'Puppet &#8211; Working with a dynamic hiera.yaml file'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-working-with-a-dynamic-hiera-yaml-file
published: true
post_date: 2015-01-13 00:00:00
---
One of the really really powerful thing with the hiera is that you can embed variables inside your hiera.yaml file to make it more dynamic. 




<blockquote>
<strong>Note:</strong> If you make any changes to the hiera.yaml file, then always restart the puppetmaster service for the changes to take affect.</blockquote>



For example, let's say you have created a new data-source file called NewData1.yaml and you want to see if hiera can successfully query it. In that case you need to update hiera.yaml to have this file included:


<pre>
 ---
:backends:
  - yaml
:yaml:
  :datadir: /etc/puppet/hieradata/yaml
:hierarchy:
  - NewData1
  - filaA
  - fileB
  - common
</pre>

After that you can run your hiera query like normal. However what if in the coming days there are several more files you want to test, e.g. NewData2.yaml, NewData3.yaml, NewData4.yaml,... and etc. In that case you will be constantly inserting yaml filenames into your hiera.yaml file, and consequently your hiera.yaml file will get longer and longer.   

However an alternative approach is to embed a parameter, that's called something like "CustomFileName" into the hiera.yaml: 


<pre>
 ---
:backends:
  - yaml
:yaml:
  :datadir: /etc/puppet/hieradata
:hierarchy:
  - "%{CustomFileName}"
  - filaA
  - fileB
  - common
</pre>

Now you can resolve this via the hiera commandline, using the <em>variable='test'</em> syntax as suggested in the hiera help info:

<pre>
[root@puppetmaster etc]# hiera --help
Usage: hiera [options] key [default value] [variable='text'...]

The default value will be used if no value is found for the key. Scope variables
will be interpolated into %{variable} placeholders in the hierarchy and in
returned values.

    -V, --version                    Version information
    -d, --debug                      Show debugging information
    -a, --array                      Return all values as an array
    -h, --hash                       Return all values as a hash
    -c, --config CONFIG              Configuration file
    -j, --json SCOPE                 JSON format file to load scope from
    -y, --yaml SCOPE                 YAML format file to load scope from
    -m, --mcollective IDENTITY       Use facts from a node (via mcollective) as scope
    -i, --inventory_service IDENTITY Use facts from a node (via Puppet's inventory service) as scope
</pre>


Let's say I have the following setup:

<pre>
[root@puppetmaster yaml]# cat /etc/puppet/hieradata/yaml/common.yaml
---

username: Jerry
[root@puppetmaster yaml]# cat /etc/puppet/hieradata/yaml/NewData3.yaml
---

username: Tom
[root@puppetmaster yaml]# cat /etc/puppet/hiera.yaml
 ---
:backends:
  - yaml
:yaml:
  :datadir: /etc/puppet/hieradata/yaml
:hierarchy:
  - %{CustomFileName}
  - filaA
  - fileB
  - common

[root@puppetmaster yaml]#

</pre>

Now if we don't pass in the value for CustomFileName, then we get:


<pre>
[root@puppetmaster yaml]# hiera username
Jerry
</pre>

Here, we ended up retrieving the value from common.yaml. 


However, if we now set a value for the filename:

<pre>
[root@puppetmaster yaml]# hiera username CustomFileName="NewData3"
Tom
</pre>

Then this time we retrieve the value from NewData3.yaml, since it is placed higher up the hierarchy from common.yaml, and it contains a match for "username". 


Now the cool thing with puppet is that by default puppet also utilizes this feature to load in facter and puppet builtin data into hiera.   i.e. it passes in:



<pre>
[root@puppetagent1 ~]# facter
architecture => x86_64
augeasversion => 1.0.0
bios_release_date => 12/01/2006
bios_vendor => innotek GmbH
bios_version => VirtualBox
blockdevice_sda_model => VBOX HARDDISK
.
.
...etc
</pre>

as well as:

<pre>
[root@puppetagent1 tmp]# puppet config print all | sort
agent_catalog_run_lockfile = /var/lib/puppet/state/agent_catalog_run.lock
agent_disabled_lockfile = /var/lib/puppet/state/agent_disabled.lock
allow_duplicate_certs = false
allow_variables_with_dashes = false
archive_file_server = puppetmaster.codingbee.dyndns.org
archive_files = false
async_storeconfigs = false
autoflush = true
autosign = /etc/puppet/autosign.conf
basemodulepath = /etc/puppet/modules:/usr/share/puppet/modules
.
.
.
</pre>

This means when puppetmaster runs hiera behind the scenes, it is actually running a really long hiera command, which hundreds of space seperated <em>variable='text'</em> value pairs. One thing to note is that each of these variables that the puppetmaster passes into hiera are not prefixed with "::" but they are prefixed in the hiera.yaml itself, this is to indicate that they are <a href="https://docs.puppetlabs.com/hiera/1/puppet.html#best-practices">top level data</a>. 

By embedding an agent's facter data (and builtin variables) into the hiera.yaml file, it means you can create a yaml data file that is specific to a particular puppet agent. A typical way of doing this is by making use of the agent's "hostname" fact, here's an example of this in action:

First we insert a facter based parameter into our hiera.yaml
<pre>
[root@puppetmaster puppet]# cat /etc/puppet/hiera.yaml
---
:backends:
  - yaml
:hierarchy:
  <strong>- %{::hostname}</strong>
  - global

:yaml:
  :datadir: /etc/hieradata/yaml
[root@puppetmaster puppet]#
</pre>


 
Here we have "%{::hostname}" which is referred to as an <a href="https://docs.puppetlabs.com/hiera/1/variables.html#interpolation-tokens">interpolation token</a>.

Next our site.pp shows:

<pre>
[root@puppetmaster puppet]# cat /etc/puppet/manifests/site.pp
node 'puppetagent1' {
  include user_account
}
</pre>

The user_account class in turn contains:

<pre>
[root@puppetmaster puppet]# cat /etc/puppet/modules/user_account/manifests/init.pp
class user_account ($username = "tempuser"){

  user { "$username":
    ensure => present,
    shell  => '/bin/bash',
  }


  file {"/tmp/$username.txt":
    ensure => file,
    content => $var,
  }

}
[root@puppetmaster puppet]#

</pre>

We don't want the username to default to "tempuser", instead we want to set this to "Tom", which we do by creating the following yaml file

<pre>
[root@puppetmaster puppet]# cat /etc/puppet/hieradata/yaml/puppetagent1.yaml
---

user_account::username: Tom
</pre>

This file is called "puppetagent1" because that's our agent's hostname:


<pre>
[root@puppetagent1 tmp]# facter hostname
puppetagent1

</pre>

Now let's confirm that we can retrieve this value from the command line:


<pre>
[root@puppetmaster puppet]# hiera user_account::username hostname="puppetagent1"
Tom
</pre>

However to eliminate ambiguity on which hiera.yaml file is used you should specify the path to your hiera.yaml file on the command line as well, e.g.:


<pre>
<strong>[root@puppetmaster puppet]# hiera user_account::username --config /etc/puppetlabs/code/hiera.yaml hostname="puppetagent1"
</strong>Tom
</pre>

Also check out the <a href="https://rubygems.org/gems/hiera_explain">hiera_explain</a> gem.

Also check out the new puppet lookup command: 

<a href="https://docs.puppet.com/puppet/latest/lookup_quick.html#if-you-already-use-hiera-in-environments">https://docs.puppet.com/puppet/latest/lookup_quick.html#if-you-already-use-hiera-in-environments</a>

Note: the puppet lookup command is quite new and experimental. 

Now when we do a puppet run, we get:

<pre>
[root@puppetagent1 /]# puppet agent -t
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Caching catalog for puppetagent1.codingbee.dyndns.org
Info: Applying configuration version '1424548177'
Notice: /Stage[main]/User_account/User[Tom]/ensure: created
Notice: /Stage[main]/User_account/File[/tmp/Tom.txt]/ensure: created
Notice: Finished catalog run in 0.10 seconds
[root@puppetagent1 /]# cat /etc/passwd | grep "Tom"
Tom:x:505:506::/home/Tom:/bin/bash
[root@puppetagent1 /]# ls -l |
</pre>

Success!!

As you can see here, this class requires a parameter to be fed into it (if the puppetmaster is unable to provide a value, then the class will default to "tempuser"). Since we are declaring the class using the include statement, it means that we are not declaring the class parameter's value in the site.pp file. The Puppetmaster will next use hiera behind the scenes to see if a matching value is stored in any of the yaml files. When puppetmaster submits the hiera requests it also passes in all the facter and puppet builtin data in the form of variable=text pairs, one of which is crucially the facter, hostname. This get's resolved by hiera, and consequently hiera tries looking for a yaml file named after the puppet agent's hostname, which in this case is  "puppetagent1". It successfully finds the yaml file because I have created it and then reads the content of it. Finally it finds a match for "username" and returns this back to the puppetmaster.   


<h2>Embed variables in your datasource</h2>
One final thing to point out is that you can take embed parameters in your actual yaml datasource files, to make them dynamic as well. It is done in exactly the same style as with the hiera.yaml file. 




<h2>Organising your datasource files</h2>

So far we have:


<pre>
[root@puppetmaster puppet]# cat /etc/puppet/hiera.yaml
---
:backends:
  - yaml
:hierarchy:
  <strong>- %{::hostname}</strong>
  - global

:yaml:
  :datadir: /etc/hieradata/yaml
[root@puppetmaster puppet]#
</pre>

In this situation, if you have thousands of puppet agents, then your /etc/hieradata/yaml folder will end up containing potentially thousands of yaml files. 


Luckily another cool trick you can do with your hiera.yaml file is organise your yaml datasource files into sub folders. This is done by specifying relative folder structures in your hiera.yaml file's hierarchy section. For example you can do:

<pre>
[root@puppetmaster puppet]# cat /etc/puppet/hiera.yaml
---
:backends:
  - yaml
:hierarchy:
  - %{::environment}/%{::hostname}
  - %{::environment}/global

:yaml:
  :datadir: /etc/hieradata/yaml
[root@puppetmaster puppet]#
</pre>

Here we used puppet's builtin variable called "environment" which is used for setting up <a href="https://docs.puppetlabs.com/puppet/latest/reference/environments.html#assigning-nodes-to-environments">directory environments</a>.

For more info, see:

<a href="https://docs.puppet.com/hiera/latest/configuring.html" rel="nofollow">https://docs.puppet.com/hiera/latest/configuring.html</a>


<a href="https://docs.puppet.com/puppet/latest/lookup_quick.html">https://docs.puppet.com/puppet/latest/lookup_quick.html</a>