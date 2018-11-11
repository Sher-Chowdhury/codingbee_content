---
ID: 2212
post_title: 'Puppet &#8211; Identifying redundant Puppet code using PuppetDB API'
author: sher
post_excerpt: >
  You can query the PuppetDB API to
  identify puppet environments, modules,
  and classes that are no longer being
  used. This is handy for housekeeping
  purposes.
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-identifying-redundant-puppet-code-using-puppetdb-api
published: true
post_date: 2017-07-28 15:29:28
---
You can query the PuppetDB API to identify which puppet environments and classes that are no longer being used. This is handy for housekeeping purposes. 

The commands covered in this tutorial needs to be run as the root user on the puppetmaster. 

Note: I am using version 4 of the puppetdb api. So you might need to tweak these commands in order for them to work on other versions. 

Also note, there is a really cool rubygem for identifying dead puppet code: <a href="https://github.com/camptocamp/puppet-ghostbuster" rel="nofollow">https://github.com/camptocamp/puppet-ghostbuster</a>



<h2>Identifying obselete control repo environments</h2>
The following command lists all the control repo environments that has one or more puppet agents attached to them: 

<pre>
$ curl -s -X GET http://localhost:8080/pdb/query/v4/nodes --data-urlencode 'pretty=true' | grep 'report_environment' | sort | uniq | awk '{print $NF}' | cut -d'"' -f2
dev1
dev2
test1
test2
staging
prod
</pre>


This is useful if you want to figure out which folders you can delete from <code>/etc/puppetlabs/code/environments/</code>. Any folders that are not listed by the above command can be deleted. 

Tip: The above command can be written as part of a house-keeping shell script, and then run as cronjob. You can also ensure the state of this cron job, along with it's shell script, via the puppetmaster's own role (roles and profiles).


<h2>Identifying obselete puppet classes in control repo environments</h2>
Assuming your code is structured according to the <a href="https://docs.puppet.com/pe/2017.2/r_n_p_intro.html" rel="nofollow">roles and profiles</a> convention. If that is so, then over time, can end up having obselete roles, in which case, here's a how to identify them for a given environment. 

First create this file:

<pre>
$ cat puppetdbquery.json
["and",
  ["=", "environment", "test"],
  ["=", "type", "Class"],
  ["=", "title", "Role::Blog"]]
</pre>

In this example I have an environment called 'test' and a role called 'Role::Blog' (which could be a role for building a wordpress server). Now while in the same directory, run the following:

<pre>
$ curl -s -G http://localhost:8080/pdb/query/v4/resources --data-urlencode 'pretty=true' --data-urlencode query@puppetdbresourcequery.json | grep certname | sort | uniq
  "certname" : "blog1.codingbee.net",
</pre>

This shows that the "Role::Blog" is being used by one puppet agent, for this environment. If this command returned empty, then it would have meant that you can delete this role's manifest. 

In the above example we use a roles based class, but the concept works for any classes.
 

<h2>Identifying redundant puppet modules</h2>

Over time, your control repo's Puppetfile can get longer and longer. And a lot of the modules listed in your Puppetfile might no longer be used by any of your puppet code. If that's the case, it might be a bit tricky to identify these redundant modules. 


Puppetdb can assist with tracking down these redundant modules. First create this file:


<pre>
$ cat puppetdbclassquery.json
["and",
  ["=", "environment", "dev1"],
  ["=", "type", "Class"],
  ["~", "title", "."]]
</pre>

Note: here we are using an environment called dev1 as our example. 

Then run the following command:

<pre>
$ curl -s -G http://localhost:8080/pdb/query/v4/resources --data-urlencode 'pretty=true' --data-urlencode query@puppetdbclassquery.json | grep title | cut -d'"' -f4 | awk 'BEGIN {FS="::"} {print $1}' | sort | uniq
Apache
Concat
Duplicity
Filebeat
Limits
Lvm
.
.
.
...etc
</pre>

This lists all the modules that are currently being used by all your puppet environment in that environment. You can now use this list to identify and delete all the modules specified in your Puppetfile. The same goes for using another modules erb templates, or static file, or custom resources. 


<strong>caution</strong>: any modules not listed above could still be in use, E.g.the not-listed module's custom facts could be used elsewhere. Therefore, treat the above list as an indication.

Once you have done the cleaning, you could also check your puppetfile's integrity by running:  

<pre>
$ puppet module list --tree --modulepath /etc/puppetlabs/code/environments/{env-name}/modules
</pre>