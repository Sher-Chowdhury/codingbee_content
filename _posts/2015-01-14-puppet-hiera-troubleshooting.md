---
ID: 265
post_title: 'Puppet &#8211; Hiera troubleshooting'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-hiera-troubleshooting
published: true
post_date: 2015-01-14 00:00:00
---
There will be times when Hiera just isn't returning the information that you are expecting from it. In these situations you need to do a bit of digging. The first place to start is looking at hiera's help info:


<pre>

[root@puppetmaster yaml]# hiera --help
Usage: hiera [options] key [default value] [variable='text'...]

The default value will be used if no value is found for the key. Scope variables
will be interpolated into %{variable} placeholders in the hierarchy and in
returned values.

    -V, --version                    Version information
<strong>    -d, --debug                      Show debugging information</strong>
    -a, --array                      Return all values as an array
    -h, --hash                       Return all values as a hash
    -c, --config CONFIG              Configuration file
    -j, --json SCOPE                 JSON format file to load scope from
    -y, --yaml SCOPE                 YAML format file to load scope from
    -m, --mcollective IDENTITY       Use facts from a node (via mcollective) as scope
    -i, --inventory_service IDENTITY Use facts from a node (via Puppet's inventory service) as scope
[root@puppetmaster yaml]#
</pre>

Note: using the "debug" is really helpful. 

Also check out the <a href="https://rubygems.org/gems/hiera_explain">hiera_explain</a> gem:



Also here's a way to do hiera lookup straight from the command line:


<pre>
PUPPET_GEM_VERSION=3.7.5 bundle exec puppet apply --parser=future -e "notice(hiera('foo', undef))"
</pre>
However this doesn't output the value of anything. but it confirms that it found something 


https://tickets.puppetlabs.com/browse/PUP-3863


See also:

puppetlabs.com/blog/debugging-hiera

http://puppetlabs.com/blog-tags/hiera

http://www.craigdunn.org/2011/10/puppet-configuration-variables-and-hiera/ (which shows how to force puppet to only use hiera data)