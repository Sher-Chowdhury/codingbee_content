---
ID: 214
post_title: >
  bundle exec rake
  lint/validate/spec/beaker
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/uncategorized/bundle-exec-rake-lintvalidatespecbeaker
published: true
post_date: 2014-12-03 00:00:00
---
befor you run a spec test, you first have to run:
<pre>bundle install</pre>
This will download all the dependencies (i.e ruby gems).

Then you do:

&nbsp;
<pre>bundle exec rake {task}</pre>
&nbsp;

This in parts means:

bundle exec - "execute the following script using the gems specified in the gemfile"

This in turn executes:
<pre>rake {task}</pre>
Here since we are calling rake it means that rake calls the rakefile, as a script, and it passes the "{task}" as an input parameter. This input parameter can take any of the following values:
<ul>
	<li>lint                     - check for good formatting, e.g. code is properly indented</li>
	<li>validate   - checks for syntax error</li>
	<li>spec         - runs the rspec tests (unit tests)</li>
	<li>beaker   - runs the beaker tests (acceptance tests, also written in rspec).</li>
</ul>
&nbsp;

If you want to see all the available input parameters for rake, do (run this while in the directory containing the gemfile):

<pre>
[root@puppetmaster dummyModule]# bundle exec rake 
rake beaker            # Run beaker acceptance tests
rake beaker_nodes      # List available beaker nodesets
rake build             # Build puppet module package
rake clean             # Clean a built module package
rake coverage          # Generate code coverage information
rake help              # Display the list of available rake tasks
rake lint              # Check puppet manifests with puppet-lint
rake spec              # Run spec tests in a clean fixtures directory
rake spec_clean        # Clean up the fixtures directory
rake spec_prep         # Create the fixtures directory
rake spec_standalone   # Run spec tests on an existing fixtures directory
rake syntax            # Syntax check Puppet manifests and templates
rake syntax:hiera      # Syntax check Hiera config files
rake syntax:manifests  # Syntax check Puppet manifests
rake syntax:templates  # Syntax check Puppet templates
rake validate          # Check syntax of Ruby files and call :syntax
[root@puppetmaster dummyModule]# 
</pre>



&nbsp;

&nbsp;

&nbsp;

&nbsp;

What this does is that

&nbsp;

&nbsp;

Each puppet module has to contain the following files:

&nbsp;
<ul>
	<li><strong>rakefile</strong> - this basically runs a sequence of tasks</li>
	<li><strong>gemfile</strong> - this contains a set of gemfiles required to run the rspec tests.</li>
	<li><strong>fixtures.yml</strong> - this contains the puppet module dependencies, i.e. one module may require another module in order for it to run. Note, this often a hidden file, located at teh top level of the module folder</li>
	<li><strong>spec_helper.rb</strong> - this contains the actual rspec code.</li>
	<li><strong>spec_acceptance.rb</strong> - this is to do with beaker</li>
</ul>
&nbsp;

&nbsp;

&nbsp;

On each puppet machine, the following exists:
<ul>
	<li>/etc/puppet/puppet.conf</li>
	<li>/etc/hiera.yaml</li>
	<li>/etc/r10k.yaml</li>
</ul>
&nbsp;

You should only have one of these files per puppet master/agent.

&nbsp;


If you want to test against a particular version of puppet, then you can do something like:

<pre>PUPPET_GEM_VERSION="~> 4.0" bundle exec rake spec</pre>

In this example it will test against the latest 4+ puppet version, e.g. 4.3.2. 

This will work if you gem file has a builtin if statement, e.g.:


<pre>$ cat ../Gemfile
source 'https://rubygems.org'

group :development, :test do
  gem 'puppetlabs_spec_helper', '1.0.1', :require => false
  gem 'rspec-puppet',           '2.3.2',  :require => false
  gem 'rspec-core',             '3.1.7',  :require => false
  gem 'puppet-lint-strict_indent-check',  :require => false
  gem 'metadata-json-lint',               :require => false
  gem 'rspec-puppet-facts',               :require => false
end

if puppetversion = ENV['PUPPET_GEM_VERSION']
  gem 'puppet', puppetversion, :require => false
else
  gem 'puppet', '~> 3.4', :require => false
end

# vim:ft=ruby
</pre>


Resources

<a href="https://www.google.co.uk/search?q=bundle+exec+rake&amp;ie=utf-8&amp;oe=utf-8&amp;aq=t&amp;rls=org.mozilla:en-GB:official&amp;client=firefox-a&amp;channel=sb&amp;gfe_rd=cr&amp;ei=9iV_VN29EMHH8ge6x4H4DA">bundle exec rake</a>