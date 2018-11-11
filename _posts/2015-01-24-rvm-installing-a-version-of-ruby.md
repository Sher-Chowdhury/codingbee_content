---
ID: 282
post_title: 'RVM &#8211; Installing a version of ruby'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/rvm-installing-a-version-of-ruby
published: true
post_date: 2015-01-24 00:00:00
---
You are now ready to <a href="https://www.ruby-lang.org/en/downloads/">install a version of ruby</a> to run on top of RMV. 

Before you install any new version of ruby to run on top of rvm, let's first confirm that there are no versions of ruby running on top of rvm to begin with:


<pre>
[sher@puppetmaster ~]$ rvm list

rvm rubies


# No rvm rubies installed yet. Try 'rvm help install'.
</pre>

Note: the version of ruby that was on the machine before RVM was installed, is not managed by ruby. More about this later. 

Now let's see what versions of ruby are available to run on inside rvm:

<pre>

[sher@puppetmaster bin]$ rvm list known | head -15
# MRI Rubies
[ruby-]1.8.6[-p420]
[ruby-]1.8.7[-head] # security released on head
[ruby-]1.9.1[-p431]
[ruby-]1.9.2[-p330]
[ruby-]1.9.3[-p551]
[ruby-]2.0.0[-p598]
[ruby-]2.1.4
[ruby-]2.1[.5]
[ruby-]2.2.0
[ruby-]2.2-head
ruby-head

</pre>

Now let's install 2.2.0, which we do using the rvm "install" action (see "rvm help" for more info about other available actions):


<pre>
[sher@puppetmaster /]$ rvm install 2.2.0
Searching for binary rubies, this might take some time.
No binary rubies available for: centos/6/x86_64/ruby-2.2.0.
Continuing with compilation. Please read 'rvm help mount' to get more information on binary rubies.
Checking requirements for centos.
Requirements installation successful.
Installing Ruby from source to: /home/sher/.rvm/rubies/ruby-2.2.0, this may take a while depending on your cpu(s)...
ruby-2.2.0 - #downloading ruby-2.2.0, this may take a while depending on your connection...
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 12.6M  100 12.6M    0     0   959k      0  0:00:13  0:00:13 --:--:--  834k
ruby-2.2.0 - #extracting ruby-2.2.0 to /home/sher/.rvm/src/ruby-2.2.0....
ruby-2.2.0 - #applying patch /home/sher/.rvm/patches/ruby/2.2.0/fix_installing_bundled_gems.patch.
ruby-2.2.0 - #configuring.........................................................
ruby-2.2.0 - #post-configuration..
ruby-2.2.0 - #compiling....................................................................................
ruby-2.2.0 - #installing............................
ruby-2.2.0 - #making binaries executable..
Rubygems 2.4.5 already available in installed ruby, skipping installation, use --force to reinstall.
ruby-2.2.0 - #gemset created /home/sher/.rvm/gems/ruby-2.2.0@global
ruby-2.2.0 - #importing gemset /home/sher/.rvm/gemsets/global.gems...........................................................
ruby-2.2.0 - #generating global wrappers........
ruby-2.2.0 - #gemset created /home/sher/.rvm/gems/ruby-2.2.0
ruby-2.2.0 - #importing gemsetfile /home/sher/.rvm/gemsets/default.gems evaluated to empty gem list
ruby-2.2.0 - #generating default wrappers........
ruby-2.2.0 - #adjusting #shebangs for (gem irb erb ri rdoc testrb rake).
Install of ruby-2.2.0 - #complete
Ruby was built without documentation, to build it run: rvm docs generate-ri
[sher@puppetmaster /]$

</pre>

As soon as this has finished all your "gem environment", "ruby --version", and "gem list" has changed!:

<pre>
[sher@puppetmaster /]$ ruby --version
ruby 2.2.0p0 (2014-12-25 revision 49005) [x86_64-linux]
[sher@puppetmaster /]$ gem environment
RubyGems Environment:
  - RUBYGEMS VERSION: 2.4.5
  - RUBY VERSION: 2.2.0 (2014-12-25 patchlevel 0) [x86_64-linux]
  - INSTALLATION DIRECTORY: /home/sher/.rvm/gems/ruby-2.2.0
  - RUBY EXECUTABLE: /home/sher/.rvm/rubies/ruby-2.2.0/bin/ruby
  - EXECUTABLE DIRECTORY: /home/sher/.rvm/gems/ruby-2.2.0/bin
  - SPEC CACHE DIRECTORY: /home/sher/.gem/specs
  - SYSTEM CONFIGURATION DIRECTORY: /home/sher/.rvm/rubies/ruby-2.2.0/etc
  - RUBYGEMS PLATFORMS:
    - ruby
    - x86_64-linux
  - GEM PATHS:
     - /home/sher/.rvm/gems/ruby-2.2.0
     - /home/sher/.rvm/gems/ruby-2.2.0@global
  - GEM CONFIGURATION:
     - :update_sources => true
     - :verbose => true
     - :backtrace => false
     - :bulk_threshold => 1000
  - REMOTE SOURCES:
     - https://rubygems.org/
  - SHELL PATH:
     - /home/sher/.rvm/gems/ruby-2.2.0/bin
     - /home/sher/.rvm/gems/ruby-2.2.0@global/bin
     - /home/sher/.rvm/rubies/ruby-2.2.0/bin
     - /home/sher/.rvm/bin
     - /usr/lib64/qt-3.3/bin
     - /usr/local/bin
     - /bin
     - /usr/bin
     - /usr/local/sbin
     - /usr/sbin
     - /sbin
     - /home/sher/bin
[sher@puppetmaster /]$ gem list

*** LOCAL GEMS ***

bigdecimal (1.2.6)
bundler (1.7.12)
bundler-unload (1.0.2)
executable-hooks (1.3.2)
gem-wrappers (1.2.7)
io-console (0.4.3)
json (1.8.1)
minitest (5.4.3)
power_assert (0.2.2)
psych (2.0.8)
rake (10.4.2)
rdoc (4.2.0)
rubygems-bundler (1.4.4)
rvm (1.11.3.9)
test-unit (3.0.8)
[sher@puppetmaster /]$

</pre>

If you want to revert back to your original setup, then don't panic, you just need to switch to the system version of ruby, which you can do using the rvm's "use" action:

<pre>
[sher@puppetmaster /]$ rvm use system
Now using system ruby.
[sher@puppetmaster /]$ ruby --version
ruby 1.8.7 (2013-06-27 patchlevel 374) [x86_64-linux]
[sher@puppetmaster /]$ gem list

*** LOCAL GEMS ***

hiera (1.3.4)
json (1.5.5)
json_pure (1.8.1)
[sher@puppetmaster /]$ gem environment
RubyGems Environment:
  - RUBYGEMS VERSION: 1.3.7
  - RUBY VERSION: 1.8.7 (2013-06-27 patchlevel 374) [x86_64-linux]
  - INSTALLATION DIRECTORY: /usr/lib/ruby/gems/1.8
  - RUBY EXECUTABLE: /usr/bin/ruby
  - EXECUTABLE DIRECTORY: /usr/bin
  - RUBYGEMS PLATFORMS:
    - ruby
    - x86_64-linux
  - GEM PATHS:
     - /usr/lib/ruby/gems/1.8
     - /home/sher/.gem/ruby/1.8
  - GEM CONFIGURATION:
     - :update_sources => true
     - :verbose => true
     - :benchmark => false
     - :backtrace => false
     - :bulk_threshold => 1000
  - REMOTE SOURCES:
     - http://rubygems.org/
[sher@puppetmaster /]$


</pre>

The <code>rvm use system</code> effectively disables rvm. 

If you want to switch to another (rvm managed) version of ruby then first find the version of ruby currently installed:


<pre>
[sher@puppetmaster /]$ rvm list

rvm rubies

 * ruby-2.2.0 [ x86_64 ]

# => - current
# =* - current && default
#  * - default

[sher@puppetmaster /]$

</pre>

And then just switch to it:

<pre>
[sher@puppetmaster /]$ rvm use 2.2.0
Using /home/sher/.rvm/gems/ruby-2.2.0
[sher@puppetmaster /]$ ruby --version
ruby 2.2.0p0 (2014-12-25 revision 49005) [x86_64-linux]
[sher@puppetmaster /]$
</pre>

As you can see it is now really easy to switch between different version of ruby, and their respective gem setups. 

You can also set a particular version of ruby as the default version, every time you start a new terminal session. For example to make version 2.2.0 as the default, you do:

<pre>
[sher@puppetmaster ~]$ rvm use --default 2.2.0
Using /home/sher/.rvm/gems/ruby-2.2.0

</pre>

Finally, as you can see above, our system version of ruby is 1.8.7, but if you do:

<pre>
[sher@puppetmaster ~]$ rvm use 1.8.7
ruby-1.8.7-head is not installed.
To install do: 'rvm install ruby-1.8.7-head'
[sher@puppetmaster ~]$
</pre>

This means that the system version of ruby isn't managed by rvm, however if you want to run this version of ruby inside rvm, then we do seperate rvm-install and then run it:



See also:


misc: RMV comes with a concepts called <a href="http://stackoverflow.com/questions/4689180/why-should-i-care-about-rvms-gemset-feature-when-i-use-bundler">"gemsets" this is a competitor to "bundler" and can be ignored</a>.