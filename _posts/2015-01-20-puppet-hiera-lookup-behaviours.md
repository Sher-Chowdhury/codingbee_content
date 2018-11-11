---
ID: 274
post_title: 'Puppet &#8211; Hiera lookup Behaviours'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-hiera-lookup-behaviours
published: true
post_date: 2015-01-20 00:00:00
---
In this tutorial we'll carry on looking at hiera as a standalone tool, and still ignore Puppet.

The default behaviour of hiera when it performs a lookup is to stop searching as soon as at is finds a match and returns that given match. 

However you can also instruct Hiera to search all the yaml files and collate all matches into an array (using option -a) or a hash (using option -h). 

<pre>
[root@puppetmaster ~]# hiera --help
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
[root@puppetmaster ~]#

</pre>

Let's now look at these 2 behaviours in action.

First off, lets lay out the ground work. Here's  what's in our hiera.yaml file and the corresponding yaml data files:

<pre>
[root@puppetmaster /]# cat /etc/hiera.yaml
---
:backends:
  - yaml

:hierarchy:
  - first
  - second
  - third
  - global

:yaml:
  :datadir: /etc/hieradata/yaml
[root@puppetmaster /]# cd /etc/hieradata/yaml/
[root@puppetmaster yaml]# ###############################
[root@puppetmaster yaml]# cat first.yaml
---

# Here's a simple mapping, aka string variable
username: homer

# Heres a nested mapping, aka hashtable
stuff:
  fruit:     pineapple
  furniture: chair
  sport:     tennis
[root@puppetmaster yaml]# ##############################
[root@puppetmaster yaml]# cat second.yaml
---

# Here's a simple mapping, aka string variable
username: marge
[root@puppetmaster yaml]# ##############################
[root@puppetmaster yaml]# cat third.yaml
---

# Here's a simple mapping, aka string variable
username: bart

# Heres a nested mapping, aka hashtable
stuff:
  vegetable: pineapple
  element:   hydrogen
  music:     tennis
[root@puppetmaster yaml]#

</pre>
   



<h3>Hiera array lookup (aka hiera_array)</h3>

Now let's try option "a":
<pre>
[root@puppetmaster yaml]# hiera -a username
["homer", "marge", "bart"]
</pre>

This behaviour only works when you are collating a collection of simple mappings, aka string variables. Hence it would give an error message if any of the username keys contained a nested mapping (aka hash table). 


<h3>Hiera array lookup (aka hiera_array)</h3>
Now let's try option "h":

<pre>
[root@puppetmaster yaml]# hiera -h stuff
{"vegetable"=>"pineapple",
 "sport"=>"tennis",
 "furniture"=>"chair",
 "fruit"=>"pineapple",
 "element"=>"hydrogen",
 "music"=>"tennis"}
[root@puppetmaster yaml]#
</pre>

This only works if all the matching keys contains a nested mapping (aka hashtable).