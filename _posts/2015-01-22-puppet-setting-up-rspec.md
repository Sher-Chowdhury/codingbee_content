---
ID: 277
post_title: 'Puppet &#8211; Setting up RSpec'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rspec-puppet/puppet-setting-up-rspec
published: true
post_date: 2015-01-22 00:00:00
---
<h2>Install RVM and Bundler</h2>
RSpec tests needs to be run in a tightly controlled environment. This will help ensure that the RSpec tests that you write/run in on your machine will also run on other people's machine too. The environment has to be controlled in the in the following context:


<ul>
	<li>RSpec test needs to be run under a known version of Ruby. The exact version is not that important, as long as we know which version our tests are contained in. The RVM gem is specifically designed for this purpose.</li>
	<li>We need to contain the exact gems (along with gem versions) that the Rspec tests has access to. The Bundler gem is specifically designed to meet this purpose.</li>
	<li>The Rspec tests needs to be executed by a non-root user. This will help to keep the rspec tests contained. The RVM and Bundler gem also requires you to run in non-root mode in order for them to properly work</li>
</ul>

Here are 2 gems that needs to be installed by the root user, at the machine level:

<ul>
	<li>rvm</li>
	<li>bundler</li>
</ul>



Both of these gems are installed at the user's machine level, i.e. from the command line we simply run:

<pre>
gem install rvm
</pre>
and:
<pre>
gem install bundler
</pre>

We have separate tutorials covering for configuring both RVM and Bundler. 

Using RVM and bundler, is not essential but it introduces a lot of versatility in managing your environment more effectively. 


All the other gems that we'll use (including the main RSpec gem) will be used enabled temporarily in order to run the rspec tests, and then disabled again.  



<h2>Making gems available to your puppet project</h2>
As mentioned earlier, RSpec is not designed to natively support testing puppet classes, resources, modules...etc. RSpec's normal behaviour is to instantiate an "object" and check whether it holds the correct property values.  This is only suitable if you are developing something in the context of the Ruby (object-oriented-programming) language. 

However there are a few gems that can be utilised to bridge this gap and allow us to use RSpec for puppet testing to great effect.

The 3 main gems you need are:

<ul>
	<li><a href="https://rubygems.org/gems/rspec-puppet/">rspec-puppet</a></li>
	<li><a href="https://rubygems.org/gems/puppetlabs_spec_helper">puppetlabs_spec_helper</a></li>
	<li><a href="https://rubygems.org/gems/puppet">puppet</a></li>

</ul>

Note, there are lot of other essential gems that we also need, e.g. rake, and rspec itself. However these gems are depenancies to the above gems so you don't need to explicitly cater for them, and they'll still get loaded in. 

This time you don't install these gems in the way you did for bundler and rvm, earlier. Instead you make these gems available to your puppet project, via the Gemfile, which we'll come to shortly.  

<h2>Prepare your Puppet module</h2>

A lot of these gems that we'll be using will require config files set up control how they work. 

The approach we're going to take is to create these config files but leave them all blank initially. Then try to run an rspec "hello world" test. This will result in various error messages showing up, and we will then fix these error messages by adding configuration data to our config files, one by one, until we eliminate all the error message and eventually get a success message. The reason I'm taking this approach is so to help you understand what the various error messages means which in turn will shed light on what config data needs to be added to the config files in order to resolve the error messages, and that consequently make you understand the roles the various config files play, and how they all fit into the bigger picture.  


then you can quickly create one by running the following command while you are in your module's folder:

<pre>puppet module generate</pre>

In our demos, we have created a new module called "demo-module"


A lot of the gems we mentioned so far have dependencies on certain config files/folders existing within your puppet module. If they are not present, then you need to create them yourself. The four config files you need in your puppet module are:

<pre>

[root@puppetmaster demo_module]# tree
.
├── .fixtures.yml           # this file (which is a hidden file)
├── Gemfile                 # this file 
├── Gemfile.lock
├── manifests
│     └── init.pp
├── Modulefile
├── Rakefile                # this file 
├── README
├── spec
│     └── spec_helper.rb      # this file 
└── tests
    └── init.pp

11 directories, 13 files
[root@puppetmaster demo_module]# 


</pre>
Note: There is a top folder called "tests" but actually doesn't house any rspec tests. Instead it is just used for <a href="https://docs.puppetlabs.com/learning/modules1.html#the-other-subdirectories">example code</a>.

Let's now take a look at each of these config files in turn:

<h3>Gemfile</h3>
This config file is used by the bundler to determine which gems (as well as which version) will be made availabe to Rspec. You then make these gems available by first downloading these gems:

<pre>
bundle install      
</pre>

The gems you download are likely to have dependencies on other gems, and hence all the dependant gems are downloaded and made available as well. 


You then force Rspec to only access these gems by prefixing your rspec-execution-command command with, "bundle exec", i.e.:

<pre>
bundle exec {rspec-execution-command}
</pre>
  

As a minimum, you can add the following into your Gemfile:

<pre>
[vagrant@puppetmaster rspecdemo]$ cat Gemfile
source 'https://rubygems.org'

gem 'rspec-puppet', :require => false
gem 'puppetlabs_spec_helper',  :require => false
gem 'puppet',  :require => false
[vagrant@puppetmaster rspecdemo]$ 
</pre>


<h4>A bit more background info about Gemfile</h4>
This section gives some more background info that you can read up on after you have managed to successfully pass your first rspec test. If you haven't passed your first test, then skip this section and come back to it later. 


However most of the official puppetlab modules such as <a href="https://github.com/puppetlabs/puppetlabs-ntp/blob/master/Gemfile">Puppetlab's ntp module</a> has something like this:


<pre>
$ cat Gemfile
source 'https://rubygems.org'
group :development, :test do
gem 'rake', :require => false
gem 'rspec-puppet', :require => false
gem 'puppetlabs_spec_helper', :require => false
gem 'rspec-system', :require => false
gem 'rspec-system-puppet', :require => false
gem 'rspec-system-serverspec', :require => false
gem 'serverspec', :require => false
gem 'puppet-lint', :require => false
end
if puppetversion = ENV['PUPPET_GEM_VERSION']
  gem 'puppet', puppetversion, :require => false
else
  gem 'puppet', :require => false
end
# vim:ft=ruby
</pre>

Notice the if-else statement. This makes it possible to use a particular gem version we want to use, by simply creating an linux environment variable called "PUPPET_GEM_VERSION" before we start the puppet run. E.g. from the command line we first do:

<pre>
$ export PUPPET_GEM_VERSION="3.7.2" 
$ env | grep "PUPPET_GEM_VERSION"
PUPPET_GEM_VERSION=3.7.2
</pre>

Inclusion of these kinds of if-else statements in the gemfile effectively means that we have more <a href="http://codingbee.net/tutorials/ruby/ruby-gemfile/" title="Ruby - GemFile">dynamic gemfile</a> which can be used in more versatile ways.  Here's an example of a <a href="https://github.com/puppetlabs/puppetlabs-ntp/blob/master/Gemfile">dynamic gemfile</a>. 

For instance can also set this value via a continuous integration tool such as Jenkins, in which case we do this is in the form of build parameters.  

Another cool alternative to CI is called Travis. This tool can accept a .travis.yml which can list multiple combinations of parameter sets, and each git commit, can result in multiple job runs, one for each parameter combo. This is a good way to test your puppet module across several combinations of gem versions and ruby versions (via rvm)

However there is a jenkins plugin that can mimic this travis feature. 

We will cover more about about automating puppet unit testing via Jenkins, later. 



<h3>Rakefile</h3>

RSpec internally runs a predefined set of tasks when trigger a test run. While these tasks work fine for a Ruby based project, they won't work for a Puppet based project. 

Therefore to bridge this gap between Rspec and puppet, we provide our custom set of tasks for Rspec to follow instead. We provide this task via the "rake" gem, which is used for task management. Hence rake reads the rakefile to see what custom sets of tasks are available for use. we can then force rspec to use our custom (puppet compatible) task rather than the default task-set. 

You then force Rspec to only run our custom (puppet compatible) tasks by prefixing your rspec-execution-command command with, "rake", i.e.:


<pre>
rake {rspec-execution-task}
</pre>

Note: "rspec-execution-task" are a series of tasks that we will make available to rake, a little further down.  

However to also need to take into account our predefined gems (i.e. those listed in the gemfile), therefore we need to prefix our rake command with "bundle exec":

<pre>
bundle exec rake {rspec-execution-task}
</pre>

However before we can run the above command we first need to tell rake where to locate the puppet-rspec specific rake tasks. Luckily puppetlabs have already created a set of predefined rake task, which it has packaged into the "puppetlabs_spec_helper" gem. Therefore all we need to do is tell rake to look inside the puppetlabs_spec_helper gem for the rake tasks. We tell this to rake by adding the following lines in the rakefile: 

<pre>
[vagrant@puppetmaster rspecdemo]$ cat rakefile 
require 'rubygems'
require 'puppetlabs_spec_helper/rake_tasks'
[vagrant@puppetmaster rspecdemo]$ 
</pre>  

Rake should now have access to rspec-puppet specific rake tasks (as well as a few other tasks), you can check this by simply running the the following:

<pre>
[vagrant@puppetmaster rspecdemo]$ bundle exec rake
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
[vagrant@puppetmaster rspecdemo]$ 
</pre>

Since we didn't following up the rake command, with a task name, it automatically listed out all the available tasks, that have been made available thanks to the puppetlabs_spec_helper gem. 




<h3>.fixtures.yaml</h3>
This file lists all the other puppet modules that your module is dependant on. During a rspec test run, these modules gets download to your modules spec/fixtures/modules folder. The puppetmaster then use this folder as a temporary pseudo modules folder (rather than using /etc/puppet/modules folder) in order to generate the catalog.

The fixtures file also tell's rspec the location of the puppet module that's under test. 

As a side-note, If you don't create the fixtures.yaml file, and just run the rspec test anyway, you'll get an error message taht looks something like this:

<pre>
[vagrant@puppetmaster rspecdemo]$ bundle exec rake spec
/usr/local/rvm/rubies/ruby-1.9.3-p551/bin/ruby -S rspec spec/classes/init_spec.rb --color
F

Failures:

  1) rspecdemo The following classes should be present in the catalog should compile into a catalogue without dependency cycles
     Failure/Error: it { should compile }                # this is the test to check if it compiles.
       error during compilation: <strong>Could not find class rspecdemo for puppetmaster.ordsvy.gov.uk on node puppetmaster.ordsvy.gov.uk</strong>
     # ./spec/classes/init_spec.rb:6:in `block (3 levels) in <top (required)>'

Finished in 0.03425 seconds
1 example, 1 failure

Failed examples:

rspec ./spec/classes/init_spec.rb:6 # rspecdemo The following classes should be present in the catalog should compile into a catalogue without dependency cycles
/usr/local/rvm/rubies/ruby-1.9.3-p551/bin/ruby -S rspec spec/classes/init_spec.rb --color failed
[vagrant@puppetmaster rspecdemo]$ 
</pre> 
Note, in my case my module is called "rspecdemo"


Now as minimum, you can add the following into your fixtures.yml:

<pre>
[vagrant@puppetmaster rspecdemo]$ cat .fixtures.yml 
fixtures:
 forge: "https//forge.puppetlabs.com"
 symlinks:
   rspecdemo: "#{source_dir}"
[vagrant@puppetmaster rspecdemo]$ 
</pre>

However if your module has dependencies to particular version of other puppet modules, then you need to specify these dependencies here too. Here are some <a href="https://github.com/puppetlabs/puppetlabs_spec_helper#fixtures-examples">sample .fixtures.yml files</a> to help understand how to do this. 

    

<h3>spec_helper.rb</h3>



This is a rspec specific config file. This is used to adjust any of the rspec core settings. What we need to do here is to make the core rspec software aware of the rspec-puppet specific syntax. Luckily there is some code in the puppetlabs_spec_helper module that bridges that gap for us. Therefore at a minimum the only think we need to add this file is:  

<pre>
require 'puppetlabs_spec_helper/module_spec_helper'
</pre>


<h3>Create folders</h3>
Under the spec folder you also have to create the following folders to keep all your rspec tests organised. 
This is accordance to the <a href="https://github.com/rodjek/rspec-puppet#naming-conventions">puppet-rspec convention</a>. Therefore in our case we create the following folders:

 
<pre>
[vagrant@puppetmaster demo_module]$ cd spec/
[vagrant@puppetmaster spec]$ mkdir classes defines functions hosts
[vagrant@puppetmaster spec]$ cd ..
[vagrant@puppetmaster demo_module]$ tree
.
├── manifests
│   └── init.pp
├── metadata.json
├── Rakefile
├── README.markdown
├── spec
│   ├── classes          # new folder
│   ├── defines          # new folder
│   ├── functions        # new folder
│   ├── hosts            # new folder
│   ├── spec_helper.rb
│   └── spec.opts
└── tests
    └── init.pp
7 directories, 7 files
[vagrant@puppetmaster demo_module]$
</pre>


We just have to create the folders for the time being since the rb files are the actual rspec tests, which we'll be writing in the next tuturial. 





<h2>Verify setup</h2>

Do:

<pre>
bundle install    # verifies that bundler is installed and gemfile is working. 

bundle exec gem list   # also verifies that bundler is installed and gemfile is working. 

rake help       # verifies that puppet compatible rake tasks are available

bundle exec rake spec     # this should pass, because no tests written yet, hence nothing to fail
</pre>


If the above preliminary checks all passes, then you can then go ahead and start writing puppet tests, which we'll do in the next lesson.  








See also:

http://rspec-puppet.com/setup/

http://terrarum.net/blog/puppet-testing-part-1.html


https://rubygems.org/gems/puppetlabs_spec_helper

http://blog.nicksieger.com/articles/2007/01/02/customizing-rspec/ (gives info about the spec_helper.rb file)