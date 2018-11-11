---
ID: 238
post_title: 'Puppet &#8211; Directory Environments'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-directory-environments
published: true
post_date: 2014-12-23 00:00:00
---
let's say you have two groups of puppet agents, these 2 sets have the following names:

<ul>
	<li>prod</li>
	<li>test</li>
</ul>

Let's now say that the same puppetmaster manages these two groups. 
In which case te site.pp file will have node definitions for both prod and test agents. 

This approach isn't a good idea, that's because, while testing, if you introduce an error into your site.pp, then that could also break prod as well.  

One way around this is to have two site.pp files. This is by setting up something called "Directory Environments". 

With "Directory Environments", you can have multiple site.pp files on the puppetmaster. These site.pp will also have exclusive access to it's own modules folder, amongst other things.


To start using "Directory Environments", you need to take the following steps:

<ol>
	<li>Configure Puppet Master to use directory environments</li>
	<li>Create your environments</li>
	<li>Assign nodes to their environments.</li>
</ol>

<h2>Configure Puppet Master to use directory environments</h2>

This involves:

<ol>
	<li>Enable Directory Environment on puppet.conf file</li>
	<li>Create a Directory Environment</li>
</ol>

<h3>Enable Directory Environment on puppet.conf file</h3>

Here we set the following parameters:

<ul>
	<li><code>environmentpath</code> - Before you set this, you first need to ensure you have a folder for this parameter to point to, e.g. I created a folder called /etc/custom-environments and set this parameter to this. I think best practice is to set this to <code>$confdir/environments</code>. Also see the <a href="https://docs.puppetlabs.com/references/latest/configuration.html#environmentpath">environmentpath</a> reference. This value must be set in the <a href="https://docs.puppetlabs.com/puppet/3.7/reference/environments_configuring.html#environmentpath">[main]</a> section.</li>
	<li><code>basemodulepath</code> - This is optional, it's a place to for storing modules that are to be used by all environments. It acts as a fallback if the environment's own module doesn't contain the module. Also see the "<a href="https://docs.puppetlabs.com/references/latest/configuration.html#basemodulepath">basemodulepath</a>" reference.</li>
	<li><code>default_manifest</code> - This is optional, also see the <a href="https://docs.puppetlabs.com/references/latest/configuration.html#defaultmanifest">default_manifest</a> referece. </li>
</ul>




<h3>Create a Directory Environment</h3>

Under the $environmentpath directory, you need to create a folder for each of your environments, hence the folder's should be named after your environment. I.e. The directory name is the same as the environment's name.

Within each of these folders, create the following folders/files:

<ul>
	<li>modules</li>
	<li>manifests</li>
	<li>environment.conf - this is optional. <a href="https://docs.puppetlabs.com/puppet/latest/reference/config_file_environment.html">environment.conf</a> overrides defaults.</li>
</ul>

For example if we want to create an environment called "test1", and our $environmentpath is equal to /etc/custom-environments, then we will end up with the following structure:

<pre>


</pre>

See also Puppetlabs page for more info about how to <a href="https://docs.puppetlabs.com/puppet/latest/reference/environments_creating.html">create directory environments</a>.

Puppetlabs also has a page covering <a href="https://docs.puppetlabs.com/puppet/latest/reference/environments_creating.html">environment.conf</a>. 

<h3>Restart PuppetMaster</h3>
Whenever you make changes to the puppet.conf, you need to to then restart the puppetmaster to load the changes:

<pre>
service puppetmaster restart
</pre>

<h3>Assign agents to environments</h3>
When ever an agents requests a catalog from the puppetmaster (i.e. doing a puppet run), it will need to tell the master what environment it belongs to. 

You can do this through <a href="https://docs.puppetlabs.com/puppet/latest/reference/environments.html#assigning-nodes-to-environments">ENC or via the agent's puppet.conf</a> file. 

We'll look at the <a href="https://docs.puppetlabs.com/puppet/latest/reference/environments_assigning.html#assigning-environments-via-an-enc">ENC approach</a> later. For now we'll just look at <a href="https://docs.puppetlabs.com/puppet/latest/reference/environments_assigning.html#assigning-environments-via-the-agents-config-file">assining agent to an environment using teh config file</a>. 








See also:
https://docs.puppetlabs.com/puppet/latest/reference/environments.html

http://puppetlabs.com/blog/git-workflow-and-puppet-environments

http://puppetlabs.com/blog/git-workflow-and-puppet-environments