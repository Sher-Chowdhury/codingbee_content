---
ID: 139
post_title: 'Puppet &#8211; Variables, If-statements, and Environment variables'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-variables-if-statements-and-environment-variables
published: true
post_date: 2014-08-13 00:00:00
---
https://docs.puppetlabs.com/learning/variables.html

&nbsp;

https://docs.puppetlabs.com/puppet/latest/reference/lang_variables.html#facts-and-built-in-variables

&nbsp;

https://docs.puppetlabs.com/puppet/latest/reference/lang_facts_and_builtin_vars.html

&nbsp;

&nbsp;

Complete list of environment variables (aka "FACTS"): <a href="https://docs.puppetlabs.com/facter/latest/core_facts.html">https://docs.puppetlabs.com/facter/latest/core_facts.html</a>

"facter" is a standalone package. This means that it isn't a puppet subcommand but is actually a command in it's own right.

If you you:

&nbsp;

[bash]# facter[/bash]

then it will output a list of all environment variable (similar to linux's "env" command).

&nbsp;

&nbsp;

In the selectors example it showed:

&nbsp;
<pre>
$apache = $operatingsystem ? {
      centos                => 'httpd',
      redhat                => 'httpd',
      /(?i)(ubuntu|debian)/ => 'apache2',
      default               => undef,
    }
</pre>
Here we are trying to determine what value that "$apache" can take. This depends on the value of the $operatingsystem (which is a builtin variable, aka fact).

&nbsp;

The third line shows "/...../". This is actually a regex. The   "?i" is acutally a regex switch that <a href="http://www.regular-expressions.info/modifiers.html">enables case-sensitivity</a>, a bit like the "-i" switch for bash's grep command.

&nbsp;

&nbsp;