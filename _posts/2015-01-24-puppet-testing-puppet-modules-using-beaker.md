---
ID: 279
post_title: 'Puppet &#8211; Testing Puppet Modules using Beaker'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-testing-puppet-modules-using-beaker
published: true
post_date: 2015-01-24 00:00:00
---
Within your beaker test script you actually write a manifest and store that manifest into a variable called "pp"

This "pp" variable is a multiline variable. 

This pp is then sent to the test-machine using the "apply_manifest" This function requires 2 parameters, the first one is "pp" and the second one is a mode. 

<h2>Puppet Exit codes</h2>
0: This means no changes
1: This means that puppetmaster was unable to generate the catalog, maybe due to a parse failure.
2: puppet agents has made changes to itself after receiving the catalog. I.e. resource changes
4: pupppet agent tried to make changes to itself but failed, i.e. resource failure. e.g. trying to create a new file in a non existance directory. 
6: (2+4), which means resource changes as well as resource failures. 

You can use beaker's "<a href="https://github.com/puppetlabs/beaker/wiki/The-Beaker-DSL-API#shell-run_script-apply_manifest-fact-etc-using-the-default-host">apply_manifests()</a>" function. You can find out more about these exitcodes using puppet's help command:


<pre>
[sher@puppetmaster ~]$ puppet help apply
.
.
* --detailed-exitcodes:
  Provide transaction information via exit codes. If this is enabled, an exit
  code of '2' means there were changes, an exit code of '4' means there were
  failures during the transaction, and an exit code of '6' means there were both
  changes and failures.

.
.
</pre>

We have a function called "apply_manifest()" This is the function we used to tell beaker whether we want are doing negative or positive testing. 

:catch_failures   - This means that we are expecting this test to pass (i.e. 0 or 2), but if anything else, i.e. 1,4, or 6, then fail the test and report the failure details.  

:catch_changes - This means that we don't expect any resource changes, i.e. we are only expecting 0, but if there are any changes, then catch them. 

:expect_failures

:expect_changes




 








https://www.google.co.uk/search?q=beaker+&ie=utf-8&oe=utf-8&gws_rd=cr&ei=1XLDVKTKHoXvUrrGgqAP#safe=off&q=beaker+puppet