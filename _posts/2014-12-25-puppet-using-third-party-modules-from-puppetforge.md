---
ID: 246
post_title: 'Puppet &#8211; Using third-party modules from Puppetforge'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-using-third-party-modules-from-puppetforge
published: true
post_date: 2014-12-25 00:00:00
---
Puppet comes with the "modules" subcommand. This command let's you do a number of things including installing modules from <a href="https://forge.puppetlabs.com/modules">puppetforge</a>, which is the official library where the wider puppet community share their modules with others. 

The modules subcommand can list all installed modules:

<pre>
[root@puppetmaster puppet]# puppet module list
/et/puppet/modules
├── adrien-alternatives (v0.3.0)
├── adrien-boolean (v1.0.1)
├── garethr-docker (v3.3.1)
├── garethr-erlang (v0.3.0)
├── iaac-eclipse_platform (v0.1.0)
....etc.
</pre>

If any modules are installed but they are dependent on other modules that haven't been installed yet, then this command will output a warning message to indicate this. 

Now let's say we want to install the <a href="https://forge.puppetlabs.com/puppetlabs/apache">apache</a> module, then we run:

<pre>
[root@puppetmaster /]# puppet module install puppetlabs-apache
Notice: Preparing to install into /git_source/puppet/environments/dev_virtualbox_v2/modules ...
Notice: Downloading from https://forge.puppetlabs.com ...
Notice: Installing -- do not interrupt ...
/etc/puppet/modules
└─┬ puppetlabs-apache (v1.2.0)
  └── puppetlabs-concat (v1.1.2)
[root@puppetmaster /]#
</pre>

Notice that the concat module has also been installed. That's because concat is a <a href="https://forge.puppetlabs.com/puppetlabs/apache/dependencies">dependency of the apache module</a>. It hasn't downlaoded the puppetlabs/stdlib module because that is already installed on our puppetmaster. 

Hence in that respect, the puppet module install command manages dependancies just like yum does with rpm packages.  

In the background, what really ends up happening is that puppet creates a folder for each module in the modules folder. This folder is just the name of the module, i.e. without the author prefix:

<pre>
[root@puppetmaster modules]# ls -l | grep -E 'apache|concat' 
drwxr-xr-x 8 root    root    4096 Nov 12 01:38 apache
drwxr-xr-x 7 root    root    4096 Oct 28 19:13 concat
</pre>

It then downlaods the module files in <a href="https://forgeapi.puppetlabs.com/v3/files/puppetlabs-apache-1.2.0.tar.gz">tar ball form</a> (the tarball link is also available on the module puppetforge page but in very small font) and extracts it into the folder.

When we then do a "puppet module list", puppet scans each modules folder for a  "<a href="https://docs.puppetlabs.com/puppet/latest/reference/modules_publishing.html#write-a-metadatajson-file">metadata.json</a>" files, and extract the module's fqdn (authorName-moduleName) along with the version. This file also lists the dependendant modules, which is how "puppet module install" new which dependancies to automatically install.   









 







https://docs.puppetlabs.com/learning/modules1.html#the-puppet-module-subcommand

https://docs.puppetlabs.com/puppet/latest/reference/modules_installing.html#using-the-module-tool