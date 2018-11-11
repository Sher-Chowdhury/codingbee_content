---
ID: 134
post_title: 'Puppet &#8211; subcommands'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-subcommands
published: true
post_date: 2014-08-06 00:00:00
---
As a linux user, you should be familiar with the regular linux commands such as cd, pwd, ls, cp, mv,...etc. 

However the Puppet software introduces it's own set of commands that you can also run on the linux terminal. For example, node, master and config. However if you try to run them, you get:

<pre>
[root@puppetmaster ~]# node
-bash: node: command not found
[root@puppetmaster ~]# master
-bash: master: command not found
[root@puppetmaster ~]# config
-bash: config: command not found
[root@puppetmaster ~]#
</pre>

Bash hasn't appeared to have recognised these commands and that's because these puppet commands are actually sub-commands of the over-arching "puppet" command. And to run these sub-command, you need to prefix "puppet" i.e. for the node subcommand we do:

<pre>
[root@puppetmaster ~]# puppet node
Error: 'node' has no default action.  See `puppet help node`.
[root@puppetmaster ~]#
</pre>

This time, it looks like bash has recognised the node command, but it failed for a different reason, which is that the node command requires further input (arguments/options) in order for it to run. We'll cover those later. 

If you just run "puppet" on it's own, you'll get a prompt to run the "help" subcommand, so if you follow this advice and run "puppet help", then you'll get a list of all the available puppet subcommands, along with a short description:

<pre>
[root@puppetmaster ~]# puppeet
-bash: puppeet: command not found
[root@puppetmaster ~]# puppet
See 'puppet help' for help on available puppet subcommands
[root@puppetmaster ~]# puppet help

Usage: puppet <subcommand> [options] <action> [options]

Available subcommands:

  agent             The puppet agent daemon
  apply             Apply Puppet manifests locally
  ca                Local Puppet Certificate Authority management.
  catalog           Compile, save, view, and convert catalogs.
  cert              Manage certificates and requests
  certificate       Provide access to the CA for certificate management.
  certificate_request  Manage certificate requests.
  certificate_revocation_list  Manage the list of revoked certificates.
  config            Interact with Puppet's settings.
  describe          Display help about resource types
  device            Manage remote network devices
  doc               Generate Puppet documentation and references
  facts             Retrieve and store facts.
  file              Retrieve and store files in a filebucket
  filebucket        Store and retrieve files in a filebucket
  help              Display Puppet help.
  inspect           Send an inspection report
  instrumentation_data  Manage instrumentation listener accumulated data.
  instrumentation_listener  Manage instrumentation listeners.
  instrumentation_probe  Manage instrumentation probes.
  key               Create, save, and remove certificate keys.
  kick              Remotely control puppet agent
  man               Display Puppet manual pages.
  master            The puppet master daemon
  module            Creates, installs and searches for modules on the Puppet Forge.
  node              View and manage node definitions.
  parser            Interact directly with the parser.
  plugin            Interact with the Puppet plugin system.
  queue             Deprecated queuing daemon for asynchronous storeconfigs
  report            Create, display, and submit reports.
  resource          The resource abstraction layer shell
  resource_type     View classes, defined resource types, and nodes from all manifests.
  secret_agent      Mimics puppet agent.
  status            View puppet server status.

See 'puppet help <subcommand> <action>' for help on a specific subcommand action.
See 'puppet help <subcommand>' for help on a specific subcommand.
Puppet v3.6.2

</pre>



One of the main reasons that puppet has adopted this "puppet {subcommand}" system rather than having each command as a standalone, is so to avoid possible conflict's with Linux's native commands. For example, one of puppet's subcommand is called "man" (see in the list above). If puppet's man command was a standalone alone command (i.e. didn't require the "puppet " prefix) then Linux would get confused on whether you are calling the native man command, or Puppet's man command. Therefore the "puppet {subcommand}" actually offers greater clarity, but at the small price of having to type out "puppet " every time you want to call one of puppet's subcommands. 

We will cover more about each of these commands as we go through this course. 


Reference

<a href="https://docs.puppetlabs.com/puppet/3.7/reference/services_commands.html#full-list-of-subcommands ">Full List of subcommands</a>