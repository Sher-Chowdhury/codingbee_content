---
ID: 286
post_title: 'Puppet &#8211; Setting up Beaker'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-setting-up-beaker
published: true
post_date: 2015-01-31 00:00:00
---
To do a beaker run, you run the following command:

<pre>
bundle install
bundle exec rspec spec/acceptance</pre>
</pre>


By default the test will run on whichever nodeset that's called "default". Hence a file called default.yml needs to exists. which we'll cover later. 


<h2>Create the "acceptance" and "nodesets" folder</h2>
In your modules folder, all your beaker stuff resides in a new folder called "acceptance" which resides under spec:

<pre>
[sher@puppetmaster demo_module]$ mkdir spec/acceptance
[sher@puppetmaster demo_module]$ mkdir spec/acceptance/nodsets
[sher@puppetmaster demo_module]$ tree
.
├── Gemfile
├── Gemfile.lock
├── manifests
│     └── init.pp
├── metadata.json
├── orig-Gemfile
├── Rakefile
├── README.md
├── spec
│     ├── acceptance        # Here's the new "acceptance"folder. 
│     │     └── nodsets       # Here's the new "nodesets"folder.
│     ├── classes
│     │     └── init_spec.rb
│     ├── defines
│     ├── fixtures
│     ├── functions
│     ├── hosts
│     └── spec_helper.rb
└── tests
    └── init.pp
</pre>

The acceptance folder will house all our beaker tests. 
The nodesets folder will house one or more yml file. Each of these yml files will contain information about the testbed environment that beaker will need to provision and run the tests on. 


<h2>Install the necessary Gems</h2>

To run beaker tests you need to install a few gems, They are:

<ul>
	<li><a href="https://rubygems.org/gems/beaker">beaker</a></li>
	<li><a href="https://rubygems.org/gems/beaker-rspec">beaker-rspec</a></li>
	<li><a href="https://rubygems.org/gems/serverspec">serverspec</a></li>
</ul>


As discussed earlier, we can install these gems from the command line using "gem install gem-name". But it is better to take the bundler approach, and get these added to your gemfile. 

Also you should add these gems to the gemfile that you created when you used for rspec testing, hence your gemfile should now look like:


<pre>
[sher@puppetmaster demo_module]$ cat Gemfile
source 'https://rubygems.org'

gem 'puppet',                 :require => false
gem 'puppetlabs_spec_helper', :require => false
gem 'rspec-puppet',           :require => false
gem 'rake', '~>10.4.2',       :require => false

# beaker related gems
gem 'beaker-rspec', :require => false
gem 'serverspec',   :require => false

[sher@puppetmaster demo_module]$
</pre>


Note: In most gemfiles like the <a href="https://github.com/puppetlabs/puppetlabs-apache/blob/master/Gemfile">apache puppet module's gemfile</a>, the beaker gem isn't explicitly declared, that's because the beaker gem is a dependency of the beaker-rspec gem. Hence beaker will get installed/used as part of the "bundle install" and "bundle exec..." commands. 


<h2>Create the "spec_helper_acceptance.rb" file</h2>


<ul>
	<li>Gemfile - it includes the beaker gems. The main beaker specific gems are beaker, beaker-rspec, and </li>
	<li>spec/acceptance/  -  all beaker _spec.rbv files are placed here. </li>
	<li>spec/acceptance/nodesets/*  - list of files for each target testbed vm. one vm per file.</li>
	<li>spec/spec_helper_acceptance.rb - this mainly is unchanged but  </li>
</ul>







See also:
http://puppetlabs.com/presentations/beaker-automated-cloud-based-acceptance-testing-alice-nodelman-puppet-labs
http://www.rubydoc.info/github/puppetlabs/beaker/Beaker/DSL/InstallUtils:install_puppet
http://www.rubydoc.info/github/puppetlabs/beaker/Beaker/Vagrant
https://github.com/puppetlabs/beaker/wiki/The-Beaker-DSL-API#install_pe-installing-pe

http://www.xkyle.com/getting-started-puppet-acceptance-tests-with-beaker/