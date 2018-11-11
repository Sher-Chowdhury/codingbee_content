---
ID: 273
post_title: 'Ruby &#8211; GemFile'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-gemfile
published: true
post_date: 2015-01-20 00:00:00
---
Ruby gemfile

the ":require => false" basically means that isn't auto required. 


this means in your rb files, you have to have lots require statements to load in the gem's utility before you can use it. 


You can also include if-else-conditions in your gem file. This gives you two ways to pass variables to be resolved in your gemfile, the first way is by setting the environment variable:




<pre>
[root@puppetmaster demo_module]# bundle exec gem list | grep "^puppet "
puppet (3.4.3)
[root@puppetmaster demo_module]# cat Gemfile
source ENV['GEM_SOURCE'] || "https://rubygems.org"
group :development, :test do
  gem 'rake',                    :require => false
  gem 'rspec-puppet',            :require => false
  gem 'puppetlabs_spec_helper',  :require => false
end
if puppetversion = ENV['PUPPET_GEM_VERSION']           # here is the if statement. 
  gem 'puppet', puppetversion, :require => false
else
  gem 'puppet', '~> 3.4.2', :require => false
end
[root@puppetmaster demo_module]# export PUPPET_GEM_VERSION="3.7.2"
[root@puppetmaster demo_module]# env | grep "PUPPET_GEM_VERSION"
PUPPET_GEM_VERSION=3.7.2
[root@puppetmaster demo_module]# bundle install
Don't run Bundler as root. Bundler can ask for sudo if it is needed, and installing your bundle as root will break this application for all non-root users on
this machine.
Fetching gem metadata from https://rubygems.org/.........
Resolving dependencies...
Using rake 10.4.2
Using CFPropertyList 2.3.0
Using addressable 2.3.6
Using archive-tar-minitar 0.5.2
.
.
.
Installing puppet 3.7.2 (was 3.4.3)
.
.

Using bundler 1.7.7
Your bundle is complete!
Use `bundle show [gemname]` to see where a bundled gem is installed.
[root@puppetmaster demo_module]# bundle exec gem list | grep "puppet "
puppet (3.7.2)
rspec-puppet (1.0.1)
[root@puppetmaster demo_module]# 

</pre>

This effectively gives up a dynamic gemfile. 


Now if we unset this variable and try again, we get:

<pre>
[root@puppetmaster demo_module]# unset PUPPET_GEM_VERSION
[root@puppetmaster demo_module]# bundle exec gem list | grep "puppet "
puppet (3.4.3)
rspec-puppet (1.0.1)
[root@puppetmaster demo_module]# 
</pre>

In the above example, we created a linux system environment variable, which got resolved inside the Gemfile, however it can also be Jenkins environment variable (i.e. build step parameter) and it will still work the same way. 

The second approach which essentially temporarily sets up the environment variable while the command is running. This means your "env" command doesn't get cluttered up with lots of settings. Here's how this is done:

<pre>

[vagrant@puppetmaster demo_module]$ PUPPET_GEM_VERSION="3.7.3" bundle install

</pre>
Notice that we didn't need to do any piping. 


This should also work with jenkins as well, I think. 

Here's an example of a <a href="https://github.com/puppetlabs/puppetlabs-ntp/blob/master/Gemfile">dynamic gemfile</a>. 


Here's another handy technique:

<pre>
source ENV['GEM_SOURCE'] || "https://rubygems.org"

group :development, :test do
  gem 'puppetlabs_spec_helper', '1.0.1',  :require => false
  gem 'rspec-puppet',           '2.3.0',  :require => false
  gem 'rspec-core',             '3.1.7',  :require => false <mark>if RUBY_VERSION =~ /^1.8/</mark>
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

installing rspec-puppet, will automatically install the latest version of rspec-core. However if you want to use a particular version of rspec-core when running ruby 1.8.x, then you include the above line along with the if condition.