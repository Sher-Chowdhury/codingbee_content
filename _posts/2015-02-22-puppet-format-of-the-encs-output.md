---
ID: 300
post_title: 'Puppet &#8211; Format of the ENC&#8217;s output'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-format-of-the-encs-output
published: true
post_date: 2015-02-22 00:00:00
---
<h2>Intro</h2>
The yaml data outputted by ENC can only have 3 high level keys, <code>classes</code>, <code>parameters</code>, and <code>environment</code>. We'll now take a look at each of these in turn. 


  
<h2>The "classes" key</h2>
We have already come across this in the previous lesson. This basically is where you write all your node definition's include statements in yaml form. However you can add class parameter values here as well, e.g.:


<pre> 
[root@puppetmaster yaml]# cat /tmp/node-definitions/puppetagent1.codingbee.dyndns.org.yaml
---

classes:
  user_account:
    username: james

[root@puppetmaster yaml]#
</pre>

This is basically the equivalent of doing a resource-like class declaration, but in yaml form. By specifying your class parameters here, it means that the puppetmaster will no longer refer to hiera to lookup the value for the "username" class parameter. 

However this approach isn't good practice because Hiera is much better suited at keeping your class parameter data organized compared to using an ENC for this.  

<h2>The "parameters" key</h2>
This basically let's you attach variables to your puppet agent. These are facter, but in this case the puppet agent's don't derive the values, instead these values are defined by the ENC. 

One possible use for this, is that you can use this way to attach metadata to your puppet agent (although <a href="http://codingbee.net/tutorials/puppet/puppet-external-facts/">external facts</a> might be a better way of doing this). For example your puppet agent may be provisioned for use in IT project called "Genesis" in order to build an IT system called "Skynet". In that case you can attach these metadata to your puppet agent like this:

<pre> 
[root@puppetmaster yaml]# cat /tmp/node-definitions/puppetagent1.codingbee.dyndns.org.yaml
---

classes:
  - user_account:

parameters:
  - project_name: Genesis
  - system_name:  Skynet    
[root@puppetmaster yaml]#
</pre>

These paremeters are then available to the puppetmaster in the same style as any order facter data. Hence you can use them as part of things like puppet if-else statements, and generating contents for files.  

for example if the user_account module's init.pp contains:

<pre>
[root@puppetmaster yaml]# cat /etc/puppet/modules/user_account/manifests/init.pp
class user_account ($username = "tempuser"){

  user { "$username":
    ensure => present,
    shell  => '/bin/bash',
  }


  file {"/tmp/testfile.txt":
    ensure => file,
    content => "The project name is: $project_name and the IT system name is $system_name. \n",
  }

}
[root@puppetmaster yaml]#
</pre>   

In that case, if we do a puppet run, we get:

<pre>
[root@puppetagent1 tmp]# cat /tmp/testfile.txt
cat: /tmp/testfile.txt: No such file or directory
[root@puppetagent1 tmp]# puppet agent -t
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Caching catalog for puppetagent1.codingbee.dyndns.org
Info: Applying configuration version '1424636188'
Notice: /Stage[main]/User_account/File[/tmp/testfile.txt]/ensure: defined content as '{md5}4a0f48e055fa3de603280a5e268d4c26'
Notice: Finished catalog run in 0.04 seconds
[root@puppetagent1 tmp]# cat /tmp/testfile.txt
The project name is: Genesis and the IT system name is Skynet.
[root@puppetagent1 tmp]#

</pre>


<h2>The "environment" key</h2>
This is linked related to the <a href="https://docs.puppetlabs.com/references/latest/configuration.html#environment">Puppet agent's environment config setting</a>, under the [main] section. 

This setting is specifically used for setting up a powerful feature called <a href="https://docs.puppetlabs.com/puppet/latest/reference/environments.html#about-directory-environments">directory environments</a>.

Normally you set this in the puppet agent's puppet.conf file, e.g.: 

<pre>
[root@puppetagent1 tmp]# cat /etc/puppet/puppet.conf
[main]
    # The Puppet log directory.
    # The default value is '$vardir/log'.
    logdir = /var/log/puppet

    # Where Puppet PID files are kept.
    # The default value is '$vardir/run'.
    rundir = /var/run/puppet

    # Where SSL certificates are kept.
    # The default value is '$confdir/ssl'.
    ssldir = $vardir/ssl

    server=puppetmaster.codingbee.dyndns.org
<strong>
    environment=test                              # here</strong>

[agent]
    # The file in which puppetd stores a list of the classes
    # associated with the retrieved configuratiion.  Can be loaded in
    # the separate ``puppet`` executable using the ``--loadclasses``
    # option.
    # The default value is '$confdir/classes.txt'.
    classfile = $vardir/classes.txt

    # Where puppetd caches the local configuration.  An
    # extension indicating the cache format is added automatically.
    # The default value is '$confdir/localconfig'.
    localconfig = $vardir/localconfig
[root@puppetagent1 tmp]#

</pre>

So that:

<pre>
[root@puppetagent1 /]# puppet config print environment
test
[root@puppetagent1 /]#
</pre>

However individually logging into all the agents to do this can be tedious. Hence, it's easier to set this centrally.