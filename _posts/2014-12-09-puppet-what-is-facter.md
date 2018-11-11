---
ID: 217
post_title: 'Puppet &#8211; What is Facter'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-what-is-facter
published: true
post_date: 2014-12-09 00:00:00
---
Puppet is two things:
<ul>
 	<li>It is a command line tool for querying node specific information</li>
 	<li>It is a place where a node specific key-value hash is stored.</li>
</ul>
Facter is a standalone tool that house's environment-level variables. In that sense, It is a bit like the bash equivalent of the "env" command, in fact there is an overlap between info stored in facts and info stored in the "env" command.

Each key-value in facter is referred to as "facts". You can view all the facts and their values using the facter command on it's own:
<pre>
$ facter
</pre>

Note, if the above comamand doesn't give the fact value that you're looking for, then also try:

<pre>
$ facter -p
</pre>


This will list all the various environment variables, and their respective values. These collection of facts comes with facter straight out of the box and are referred to as core facts. However you can add your own "custom facts" to this collection, which we will cover later.

If you just to view one variable's value, then you can just do:
<pre>facter  {variable-name}
</pre>
For example:
<pre>[root@puppetmaster ~]# facter virtual
virtualbox
</pre>


Also some facter data is in a nested structure, and to retrieve a nested fact, then you need to give the fully qualified name. For example here's an example of a nested fact:

<pre>
$ facter -p os
{
  architecture => "x86_64",
  family => "RedHat",
  hardware => "x86_64",
  name => "CentOS",
  release => {
    full => "6.8",
    major => "6",
    minor => "8"
  },
  selinux => {
    enabled => false
  }
}
</pre>

Now if you want to retrieve the 'full' info:


<pre>
$ facter -p os.release.full
6.8
</pre>

However your puppet manifests you would refer to this fact like this:

<pre>
facts['os']['release']['full']
</pre>



However the key thing that makes facter important to puppet, is that facters facts are available throughout your puppet code as "global variables". Here is an example:
<pre>[root@puppetmaster modules]# tree user_account
user_account
└── manifests
    └── init.pp

1 directory, 1 file
[root@puppetmaster modules]# cat user_account/manifests/init.pp 
class user_account {

  user { 'homer':
    ensure =&gt; 'present',
    uid =&gt; '121',
    shell =&gt; '/bin/bash',
    home =&gt; '/home/homer',
  }


  file {'/tmp/testfile.txt':
    ensure =&gt; file,
    content =&gt; "the value for the 'osfamily' fact is: $osfamily \n",
  }

}
[root@puppetmaster modules]# puppet agent --test
Notice: /Stage[main]/Activemq::Service/Service[activemq]/ensure: ensure changed 'stopped' to 'running'
Info: /Stage[main]/Activemq::Service/Service[activemq]: Unscheduling refresh on Service[activemq]
Notice: Finished catalog run in 4.09 seconds
[root@puppetmaster modules]# cat /tmp/testfile.txt 
the value for the 'osfamily' fact is: RedHat 
[root@puppetmaster modules]# facter osfamily
RedHat
[root@puppetmaster modules]# 

</pre>
Notice here, that we didn't define $osfamily, we just used it like it was a normal variable. However there are <a href="https://docs.puppetlabs.com/puppet/latest/reference/lang_facts_and_builtin_vars.html#accessing-facts-from-puppet-code">other ways to refer to a fact in your puppet code</a>:
<ul>
 	<li><code>$fact_name</code></li>
 	<li><code>$::fact_name</code> - this is better practice</li>
 	<li><code>$facts['fact_name'] - this is best practice, put only works from puppet 3.7 onwards</code></li>
</ul>
If

In puppet, each of these environment variables are referred to as "facts". There are 3 types of facts:

- core facts
- custom facts
- external facts

All core facts are defined in the top level scope and are accessible anywhere in your code.

You can create your own facter on the <a href="http://puppetlabs.com/blog/facter-part-1-facter-101">bash command line</a> like this:
<pre>export FACTER_{facter_name}="string"    # e.g.:

[root@puppetmaster /]# export FACTER_alphabet="abcdefg" 
[root@puppetmaster /]# facter alphabet
abcdefg
[root@puppetmaster /]# 


</pre>
There is another place that you can access environment, these are <a title="Puppet - The Puppet main config file (puppet.conf)" href="http://codingbee.net/tutorials/puppet/puppet-puppet-main-config-file-puppet-conf/">puppet specific config settings</a>, which are accessble through:
<pre>puppet config print

http://serverfault.com/questions/725595/extract-nested-hash-facts-from-puppets-facter-command-line-tool-how?newreg=03145631d44641e688bb6ef527e91600
</pre>
http://terrarum.net/blog/puppet-infrastructure.html

https://docs.puppetlabs.com/facter/latest/fact_overview.html#

https://docs.puppetlabs.com/puppet/latest/reference/lang_facts_and_builtin_vars.html

https://docs.puppetlabs.com/facter/latest/core_facts.html

https://docs.puppetlabs.com/facter/latest/custom_facts.html