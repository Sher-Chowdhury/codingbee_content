---
ID: 260
post_title: 'Puppet &#8211; Using Hiera on it&#8217;s own'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-using-hiera-on-its-own
published: true
post_date: 2015-01-06 00:00:00
---
To get a good handle on hiera, let's forget about Puppet for the time being and just look at hiera on it's own. 

Hiera is a tool that lets you query data files and returns the data that matches your query. Hiera can only query two types of data files, yaml files and json files. It can potentially search through a bunch of data files to find a match. It will search through these files in a particular order, and will only stop searching once it has found a match, or has run out data files to look in.    

Hiera has it's own config file, called hiera.yaml. This file has 3 main settings, they are ":backend", ":hierarchy", and ":datadir". We'll cover more about this file later. 

Before you can start using hiera, you first need to tell hiera (via the hiera.yaml file):
  
<ol>
	<li>What types of files to search through (i.e. yaml files, or json files, or both). We specify this under the ":backend" setting.</li>
	<li>What are the names of these data files.  We specify this under the :hierarchy setting.</li>
	<li>In what order to look look through these files.  We specify this under the ":hierarchy" setting, (and also in the ":backend" setting which dictates which file type should be scanned first)</li>
	<li>In which directory all the data files are located.  We specify this under the ":datadir" setting.</li>
</ol>

All this information is provided to Hiera via the hiera.yaml file:

<pre>
[root@puppetmaster ~]# rpm -qc hiera
/etc/hiera.yaml
[root@puppetmaster ~]#
</pre>

Let's say the hiera.yaml file contains:

<pre>
[root@puppetmaster yaml]# cat /etc/hiera.yaml
---
:backends:
  - yaml
  - json

:hierarchy:
  - fileA
  - fileB
  - global

:yaml:
  :datadir: /etc/hieradata/yaml

:json:
  :datadir: /etc/hieradata/json
</pre>

In our case, this config file instructs hiera that whenever it receives a lookup request:

<ul>

	<li>It must first search through all yaml files, before looking through all the json. That's because the :backends section lists yaml first and then json.</li>
	<li>It must only scan for yaml files stored in the <code>/etc/hieradata/yaml</code>. That's as specified by the :yaml setting's :datadir sub-setting. </li>
	<li>It must only scan for json files stored in the <code>/etc/hieradata/json</code>. That's as specified by the :json setting's :datadir sub-setting. </li>
	<li>It therefore needs to search through the following files, in the following order until it hit's a match:
	<ol>
	      <li><code>/etc/hieradata/yaml/fileA.yaml</code></li>
              <li><code>/etc/hieradata/yaml/fileB.yaml</code></li>
              <li><code>/etc/hieradata/yaml/global.yaml</code></li>
	      <li><code>/etc/hieradata/json/fileA.json</code></li>
              <li><code>/etc/hieradata/json/fileB.json</code></li>
              <li><code>/etc/hieradata/json/global.json</code></li>              
        </ol>
        The filenames are as defined by the <code>:hierarchy</code> setting. As you can see, this ordering is dictated by the :backend setting, followed by the :hierarchy setting. 
        </li>
</ul>


Now let's create the following yaml file:

 
<pre>
[root@puppetmaster /]# cat /tmp/testfile.yaml
---
dad: homer
[root@puppetmaster /]#

</pre>

Now we want to see if hiera can retrieve the value for the "dad" item, which in this case is "homer". Hence we send a lookup request to hiera like this:

<pre> 
[root@puppetmaster hiera]# hiera dad
nil
[root@puppetmaster hiera]#
</pre>

The word "nil" means that hiera searched the yaml and json files and failed to find a match. That's to be expected since we have placed our yaml file in the wrong directory, and hence hiera ignored it. In fact, we haven't even created the correct directory. Let's now create the directory and move the yaml file into this directory. Then do the lookup request again:

<pre>
[root@puppetmaster /]# mkdir -p /etc/hieradata/yaml
[root@puppetmaster /]# mv /tmp/testfile.yaml /etc/hieradata/yaml/
[root@puppetmaster /]# hiera dad
nil
[root@puppetmaster /]#
</pre>
 
Still no luck, that's because hiera is looking for data files with specific names. Our yaml file's name didn't match any of these, that's why hiera once again ignored our file. To fix this, let's rename our testfile.yaml to a matching name, e.g. global.yaml:


<pre>
[root@puppetmaster /]# mv /etc/hieradata/yaml/testfile.yaml /etc/hieradata/yaml/global.yaml
[root@puppetmaster /]# hiera dad
homer
[root@puppetmaster /]#
</pre>

Success!

Notice that fileA.yaml and fileB.yaml didn't exist. In these instances Hiera would simply ignore these filenames and skip to the next available file. In other words we are seeing the fallback approach in action.  Let's now create a file called FileA.yaml which has the following content:

<pre>
[root@puppetmaster yaml]# pwd
/etc/hieradata/yaml
[root@puppetmaster yaml]# ls -l
total 8
-rw-r--r--. 1 root root 25 Jan  9 21:41 fileA.yaml
-rw-r--r--. 1 root root 15 Jan  7 16:00 global.yaml
[root@puppetmaster yaml]# cat fileA.yaml
---
dad: Homer J Simpson
[root@puppetmaster yaml]# cat global.yaml
---
dad: homer
[root@puppetmaster yaml]#
</pre>

Now if we repeat the hiera lookup for "dad", we get:

<pre>
[root@puppetmaster /]# hiera dad
Homer J Simpson
</pre>

This time, it returned the value from fileA.yaml rather than global.yaml. That's because fileA.yaml is listed higher up the lookup order (i.e. first in the list) than global.yaml (which is third on the list). You can think of this as a "fall back" style to locating data.