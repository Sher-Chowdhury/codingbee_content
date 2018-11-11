---
ID: 280
post_title: 'Puppet &#8211; What is Beaker'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-what-is-beaker
published: true
post_date: 2015-01-24 00:00:00
---
beaker lets you create an environment apply the manifests, reports the results and trash the environment again. In preparation for the next test. This means that you are always using a consistent and known starting (i.e. a fresh environment).

This saves the need to force your test bed env exactly back to how it was before.

beaker can test:
- one module and all it's indiviual components.
- bunch of modules
- 2 sets of module, one to set up a db server and another for a web server and then get them together to one another.
- can test all this in a test environment with real machines.

drawbacks - it is relativel slow.

https://github.com/puppetlabs/beaker/wiki/How-to-Write-a-Beaker-Test-for-a-Module

All the following modules contains beaker tests:

&nbsp;

&nbsp;
<ul>
	<li>puppetlabs-apache</li>
	<li>puppetlabs-mysql</li>
	<li>puppetlabs-ntp</li>
	<li>puppetlabs-concat</li>
	<li>puppetlabs-inifile</li>
</ul>

along with all the rest of the puppetlab modules. 

Note: you could have a go at running one of the above modules beaker test just to practice. 

To do a beaker run, you run the following command:

<pre>
bundle install
bundle exec rspec spec/acceptance</pre>
</pre>

Note, unlike puppet-rspec unit tests which are executed via "rake", beaker tests are actually run directly using the "rspec" command, and hence there are no rake-tasks and consequently rake is not used either for running beaker tests.  


By default the test will run on whichever nodeset that's called "default"


In your modules folder, all your beaker stuff resides in a new folder called "acceptance" which resides under spec:

<pre>
[sher@puppetmaster demo_module]$ mkdir spec/acceptance
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
│     ├── acceptance          # Here's the new folder. 
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





for example in ntp module, you have specs to check:

- that you can managed the service
- can install packages correctly
- ntp_config_spec.rb - this checks the configuration stuff. 
- ntp_parameters_spec.rb - this where most of the stuff are. It basically runs tests for every single parameter that ntp module accepts. e.g. can I pass in servers. 




when you do run a beaker test, what actually happens is:

<ol>
	<li>test vm gets booted</li>
	<li>waits for boot to complete</li>
	<li>ssh into the vm</li>
	<li>run the spec tests. </li>
</ol>


There are also <a href="https://github.com/puppetlabs/beaker/wiki/How-to-Write-a-Beaker-Test-for-a-Module#supported-env-variables">some environment level variables settings</a> that you can apply, by simply creating environment variables of a certain name. Alternatively you can pass these settings directly into your beaker-test-run command. This means that the environment variables are set up only temporarily while the beaker tests are running, and disappears after it has finished. This is done by prefixing the variable definition to the main beaker commands, e.g.:


<pre>
BEAKER_SET="a-value" bundle exec rspec spec/acceptance 
</pre>




also beaker quite a general tool and is not specific to puppet, i.e. you can write beaker tests for chef. 




Serverspec - this is just used as a checking tool. It logs into a machine (which can be a production machine) and does lots and lots of checks. Then it returns the results. It doesn't, or better yet, can't make any changes to server. It only checks, and does'nt actually make any changes to the machine. Hence you can think of it like a "server unit testing framework". This makes it perfect for inspecting production servers. But you might not be able to use it to generate any reports about versions of stuff installed, becuase you have to tell it check for a specific version of a thing. 

Therefore, Serverspec is a only a subset of what Beaker does, Where Beaker does the following:

<ol>
	<li>spins up a new machine</li>
	<li>applies the manifests</li>
	<li>check the outcome - which is the only part that serverspec on its own does</li>
	<li>destroys the used vm</li>
</ol>

Because of this, our beaker test scripts actually ends up including serverspec code in them too.  



To set up your module you need to have the following things in place in your module's folder:

<ul>
	<li>Gemfile - it includes the beaker gems. The main beaker specific gems are beaker, beaker-rspec, and </li>
	<li>spec/acceptance/  -  all beaker _spec.rbv files are placed here. </li>
	<li>spec/acceptance/nodesets/*  - list of files for each target testbed vm. one vm per file.</li>
	<li>spec/spec_helper_acceptance.rb - this mainly is unchanged but  </li>
</ul>