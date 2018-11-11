---
ID: 276
post_title: 'Puppet &#8211; What is RSpec'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rspec-puppet/puppet-what-is-rspec
published: true
post_date: 2015-01-22 00:00:00
---
<a href="http://rspec.info/">RSpec is a BDD unit testing framework</a> that is designed for testing ruby code. 

When we run a RSpec test scripts against our puppet code we are working on, the following takes place:

<ol>
	<li>RSpec temporarily generates it's own site.pp file, and then instructs the puppetmaster to use make use of that site.pp file rather than the normal site.pp file. </li>

	<li> RSpec also generates the modules folder which houses the main puppet module (that is to be tested) along with any puppet modules that it depends on. It then instructs the puppetmaster to use the new modules folder rather than the old one</li>

	<li>RSpec masquarades as a puppet agent and submits a catalog request to the master.</li>
	<li>The puppetmaster then requests facter data from the puppet agent (ie, rspec)</li>
       <li>The puppetmaster then generates the catalog and sends it to the puppet agent (i.e., RSpec)</li>
	<li>After RSpec retrieves the catalog, it then analyses it and checks whether it contains what it should contain. </li>
	<li>After that RSpec deletes the catalog, get the puppetmaster to point back to the original site.pp and modules folder again. Then, finally, it reports back the tests results. </li>

</ol>


As you can see from the above flow, the catalog never actually get's applied to any agents. Hence in RSpec, we actually do "dry runs". This means that rspec testing are much faster to run, compared to other types of tests, e.g. acceptance testing. 







RSpec’s normal behaviour is to instantiate an “object” and check whether it holds the correct property values. This is only suitable if you are developing something in the context of the Ruby (object-oriented-programming) language, where a "class" is something that you can instantiate an object from.  


Hence, Rspec straight out of the box, is not natively designed to test puppet code. That's because Puppet code is written using <a href="https://docs.puppetlabs.com/puppet/latest/reference/lang_summary.html">Puppet's own Domain Specific Language (DSL)</a>, rather than Ruby. The DSL has it's own concepts such as puppet resources, puppet classes, puppet modules....etc. 

However the good news is that there are a few gems available that can bridge this gap between rspec and puppet, and allow us to use rspec to test puppet code, the main gems are:

<ul>
	<li><a href="https://rubygems.org/gems/rspec-puppet/">rspec-puppet</a> - introduces the rspec syntax for writing puppet rspec tests. </li>
	<li><a href="https://rubygems.org/gems/puppetlabs_spec_helper">puppetlabs_spec_helper</a> - provides a mechanism to run the rspec tests from the command line. </li>
</ul>

We'll cover installation of these gems in the next lesson. 

RSpec itself, like all ruby related tools, is available in the form of the<a href="https://rubygems.org/gems/rspec">RSpec ruby gem</a>, which makes it really easy to install.

These gems in turn are dependant on other gems which are also installed as part of install rspec e.g.:

<pre>

[root@puppetmaster demo_module]# gem dependency puppetlabs_spec_helper
Gem puppetlabs_spec_helper-0.8.2
  mocha (>= 0)
  puppet-lint (>= 0)
  puppet-syntax (>= 0)
  rake (>= 0)
  rspec (>= 0)
  rspec-puppet (>= 0)

[root@puppetmaster demo_module]# 

</pre>

We'll cover how to install these gems in the next lesson.