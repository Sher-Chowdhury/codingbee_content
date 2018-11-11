---
ID: 257
post_title: 'Puppet &#8211; The hiera.yaml config file'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-the-hiera-yaml-config-file
published: true
post_date: 2015-01-03 00:00:00
---
If you followed the previous tutorial, then hiera should now be installed. Therefore you should now find a new config file present on your system, which is called hiera.yaml. This file should be located at: 

<pre>
[root@puppetmaster /]# rpm -qc hiera
/etc/hiera.yaml
[root@puppetmaster /]# ls -l /etc/hiera.yaml
-rw-r--r--. 1 root root 314 Jun  6  2014 /etc/hiera.yaml
[root@puppetmaster /]#
</pre>

Hiera is a standalone command line utility. The hiera.yaml config file is used to control how the hiera command operates when we use hiera on it's own. However, in practice we only  troubleshooting only. The main purpose of Hiera is that it is used by Puppet. When puppet uses hiera, it instructs hiera to look for the hiera.yaml file in "$confdir/hiera.yaml" instead. You can get the $confdir value like this:    

<pre>
[root@puppetmaster /]# puppet config print confdir
/etc/puppet
[root@puppetmaster /]# ls -l /etc/puppet/hiera.yaml
ls: cannot access /etc/puppet/hiera.yaml: No such file or directory
</pre>

As you can see, in my case the hiera.yaml is not where puppet expects to find it. We can therefore move the /etc/hiera.yaml to /etc/puppet/hiera.yaml. However in doing so it will stop the hiera standalone from working. Therefore the best option is to create a symbolic link instead:

<pre>
[root@puppetmaster /]# ls -l /etc/puppet/hiera.yaml
lrwxrwxrwx. 1 root root 15 Jan  5 20:01 /etc/puppet/hiera.yaml -> /etc/hiera.yaml
</pre>


By default, the content of this file should look something like:

An alternative to creating symbolic links is to set the <a href="https://docs.puppetlabs.com/references/latest/configuration.html#hieraconfig">hiera_config</a> in the puppet.conf file.

<pre>
[root@puppetmaster /]# cat /etc/puppet/hiera.yaml
---
:backends:
  - yaml
  - json
:hierarchy:
  - defaults
  - "%{clientcert}"
  - "%{environment}"
  - global
:logger: console
:yaml:
# datadir is empty here, so hiera uses its defaults:
# - /var/lib/hiera on *nix
# - %CommonAppData%\PuppetLabs\hiera\var on Windows
# When specifying a datadir, make sure the directory exists.
  :datadir:
[root@puppetmaster /]#
</pre>




This config file's content is in the form of the <a href="http://www.yaml.org/YAML_for_ruby.html">yaml structure</a>. 

As you can see, we have a few settings that are in the form of ruby symbols:

<ul>
	<li>:backends</li>
	<li>:hierarchy</li>
	<li>:logger</li>
	<li>:yaml</li>
</ul>


Let's assume, that on array called "hiera" exists which stores all this information in this config file. And each setting is an array item in this object. With this in mind, lets take a look at each setting in turn:

<h3>:backends setting</h3>

This is actually <a href="http://www.yaml.org/YAML_for_ruby.html#sequence_in_a_mapping">hash table where the value is in the form of an array</a>. Also this hash table only contains 1 key-value pair, although the array (i.e. the value) contains two items:	

For example:

<pre>
PS C:\Windows\system32> irb
DL is deprecated, please use Fiddle
irb(main):020:0* hiera = { 'backend' => ['yaml','json'] }
=> {"backend"=>["yaml", "json"]}
irb(main):021:0> hiera['backend']
=> ["yaml", "json"]
irb(main):022:0> hiera['backend'][0]
=> "yaml"
irb(main):023:0> hiera['backend'][1]
=> "json"
irb(main):024:0>
</pre>

This setting let's you choose what kind of config files it can work with. If you plan to write all your data config files in yaml form, then this should suffice. In this example hiera will first lookup the data by first scanning all available yaml files, if it can find what it is after then it will fallback to searching all available json files (if any are available).

Note, if this setting isn't specified, then the default value is "yaml" only.



<h3>:logger setting</h3>

This is a hash table with a single key-value pair, and where the value is a string.  

<pre>
irb(main):028:0* hiera = { 'logger' => 'console' }
=> {"logger"=>"console"}
irb(main):029:0> hiera['logger']
=> "console"
irb(main):030:0>
</pre>

Hiera comes with 3 built-in loggers:

<ul>
	<li>console - here messages go directly to logger.</li>
	<li>puppet - here message go directly to puppet's logging system</li>
	<li>noop - here messages are silenced</li>
</ul>


Note, if this setting isn't specified, then the default value is "console". However puppet overrides this and set's it to puppet. 

<h3>:hierarchy setting</h3>

This is actually <a href="http://www.yaml.org/YAML_for_ruby.html#sequence_in_a_mapping">hash table where the value is in the form of an array</a>. Also this hash table only contains 1 key-value pair, although the array (i.e. the value) contains 4 items.	

<pre>
will add code snippet later.
</pre>

The <a href="https://docs.puppetlabs.com/hiera/1/variables.html#interpolation-tokens">%{...} syntax</a> is used to refer to any <a href="https://docs.puppetlabs.com/hiera/1/variables.html#from-puppet">facter or puppet built-in variables</a> that are available. If non are available then they are treated as null. 

The hierarchy is actually a list of generic filenames, generic in the sense that the names doesn't include the extension, so in our case, for the "defaults" entry it can refer to a defaults.yaml or defaults.json file.   

Since we can dynamically generate these files names. We can create node specific .yaml files, by include the %{hostname} as a possible generic filename. 

We'll cover more about hierarchy in the next post. 




<h3>:yaml, :json, and other backend settings</h3>
In our :backend settings, we enabled the :yaml and :json backend settings.

For each enable backend settings, you need to specify further settings specific to the ones that are enabled. for each backend setting, we need to specify the ":datadir" sub-setting. This setting specifies the folder that contain the specified data files. For example, for :yaml's :datadir setting, it is the path to the folder that contains all the .yaml files. This is also a good place to use the <a href="https://docs.puppetlabs.com/hiera/1/configuring.html#yaml-and-json">environment variable</a>. 



See also
https://github.com/puppetlabs/hiera/blob/master/docs/tutorials/getting_started.md