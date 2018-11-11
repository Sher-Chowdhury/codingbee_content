---
ID: 278
post_title: 'Puppet &#8211; Testing Puppet Modules using RSpec'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rspec-puppet/puppet-testing-puppet-modules-using-rspec
published: true
post_date: 2015-01-23 00:00:00
---
RSpec is all about unit testing. That means we test the class without the need for changing the state of any puppet agents, i.e. it's a bit like a dry run. When we run an rspec tests, all that happens is that the rspec test provides a set of dummy (facter/puppet-variables/hiera) data to the puppetmaster via the "let" statement. The puppetmaster then generates the catalog using these inputs and then it sends it to RSpec instead of puppet-agent. RSpec then reviews the catalog to check that it contains what it "should" contain. 

If you use this approach then you can nearly write your entire puppet module without needing to run the module against a puppet agent, which is best to do much later on as part of <a href="http://codingbee.net/tutorials/puppet/puppet-testing-puppet-modules-using-beaker/" title="Puppet - Testing Puppet Modules using Beaker">acceptance testing using Beaker</a>. 


<h2>RSpec test writing approach</h2>

We will take the following (BDD) approach to writing rspec tests:

<ol>
	<li>write the rspec code</li>
	<li>Fail the Rspec test</li>
	<li>Update the puppet code</li>
	<li>Re-run the rspec test (to confirm that it now passes)</li>
</ol>

If step 4 fails, then we need to go back to step 1 or 3 to try to fix the issue. That's because a failing test could be caused by issues with the rspec or code. 

This is also the BDD approach to software development, i.e. you don't write your whole puppet module first, and then write your rspec tests as an afterthought. Instead you write your rspec code first and then follow it up with your puppet code. We will follow this process in the upcoming walkthroughs.


<h2>Preparing the puppet module for Rspec testing</h2>
The best way to explain how to write rspec tests is by walking through a series of Rspec tests in action. In order to do this, we'll first create a dummy puppet module called "demo_module".

<pre>
puppet module generate codingbee-demo_module</pre>
 

If you have done everything as expected, then your module should look something like this:

<pre>
[vagrant@puppetmaster demo_module]$ tree
.
├── manifests
│   └── init.pp
├── metadata.json
├── Rakefile
├── README.markdown
├── spec                    # This is the folder that will house all rspec tests.
│   ├── spec_helper.rb
│   └── spec.opts
└── tests                   # you can ignore this folder and it's content.
    └── init.pp
3 directories, 7 files
[vagrant@puppetmaster user_account]$
</pre>
 

Note: There is a top folder called "tests" but it actually doesn't house any rspec  tests. Instead it is just used for example code.

 

Now the first thing you need to set up the folder tree structure under spec, according to the <a href="https://github.com/rodjek/rspec-puppet">puppet-rspec folder convention</a>. We just have to create the folders for the time being since the .rb files are the actual rspec tests, which we'll be writing later on. 

 

Therefore in our case we create the following folders:

<pre> 
[vagrant@puppetmaster user_account]$ cd spec/
[vagrant@puppetmaster spec]$ mkdir classes defines functions hosts
[vagrant@puppetmaster spec]$ cd ..
[vagrant@puppetmaster user_account]$ tree
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
[vagrant@puppetmaster user_account]$
</pre>
 

At this point we are now ready to write our first rspec test!


<h2>Learning from examples</h2>

The best way to learn how to write rspec tests is by walking through a series of RSpec examples, and introduce things rspec concepts and syntax along the way. In order to do this, we’ll first create a dummy puppet module called “demo_module”.


<strong>Test 01:</strong> It should compile
<strong>Test 02:</strong> Check for main class
<strong>Test 03:</strong> Check for subclass
<strong>Test XX:</strong> Check for subclass in another folder
<strong>Test 04:</strong> Check the number of classes
<strong>Test 05:</strong> Check for a resource
<strong>Test 06:</strong> Check the number of resources
<strong>Test 07:</strong> Check for resource attributes 
<strong>Test 08:</strong> Check for class parameters
 
 

We will be using the same demo_module throughout, that means that as we progress through the above test cases, our rspec and puppet code bases will get progressively bigger. However as long as we stick to the rspec convention, everything should remain manageable/maintainable. 







<h3>Test 01: It should compile</h3>
This is the <a href="http://rspec-puppet.com/matchers/">most basic test</a> you can run. Essentially it checks whether the puppet master can process all the manifests (.pp files) and at the very minimum, generate a catalog, irrespective of what catalog's content is. 

Let's say the init.pp file's content is:
 

<pre>
[root@puppetmaster demo_module]# cat spec/classes/init_spec.rb
require 'spec_helper'                
describe 'demo_module' do
  context 'The following classes should be present in the catalog' do
    
    it { should compile }                # this is the test to check if it compiles. 
  
  end
 
end
[root@puppetmaster demo_module]# 
</pre>

We've introduced a few new things here, lets break all this down:

<ul>
	<li><code>require 'spec_helper'</code> - In order to use rspec to test puppet code, all your rspec tests (i.e. "_spec.rb" files ) needs have this line at the very top</li>
	<li><code>it { .... }</code> - This is an actual low level thing that we want to test. In Rspe, this it-block is also referred to as an "example" of something specific we want to test.</li>
	<li><code>context 'a description' do...end</code> - this is used to group together set of related tests, aka it-blocks, to help keep things organised. It also provides an human friendly description. </li>
</ul>



now let's say our init.pp file shows:

<pre>
[root@puppetmaster demo_module]# cat manifests/init.pp
class demo_module {

  xxxx

}
[root@puppetmaster demo_module]# 
</pre>

Note: For the purpose of this demo we added "xxx" in order to purposely make the test fail. 

<pre>
[root@puppetmaster demo_module]# bundle exec rake spec
/usr/local/rvm/rubies/ruby-1.9.3-p551/bin/ruby -S rspec spec/classes/init_spec.rb --color
F

Failures:

  1) demo_module The following classes should be present in the catalog 
     Failure/Error: should compile
     Puppet::Error:
       Syntax error at '}' at /git_source/puppet/environments/dev_virtualbox_v2/modules/demo_module/spec/fixtures/modules/demo_module/manifests/init.pp:5 on node puppetmaster.ordsvy.gov.uk
     # (eval):3:in `_racc_yyparse_c'
     # (eval):3:in `yyparse'
     # ./spec/classes/init_spec.rb:5:in `block (3 levels) in <top (required)>'

Finished in 0.03787 seconds
1 example, 1 failure

Failed examples:

rspec ./spec/classes/init_spec.rb:4 # demo_module The following classes should be present in the catalog 
/usr/local/rvm/rubies/ruby-1.9.3-p551/bin/ruby -S rspec spec/classes/init_spec.rb --color failed
[root@puppetmaster demo_module]# 

</pre>

Now if we remove the "xxx" typo from init.pp and then rerun the test, we get:

<pre>
[root@puppetmaster demo_module]# bundle exec rake spec
/usr/local/rvm/rubies/ruby-1.9.3-p551/bin/ruby -S rspec spec/classes/init_spec.rb --color
.

Finished in 0.0702 seconds
1 example, 0 failures
[root@puppetmaster demo_module]# 
</pre>
 









<h3>Test 02: - Check for the presence of the main class</h3>

The main class, is the class that's defined in the module's init.pp file. Hence in this case we want to test for the presence of the "demo_module" class.  

Now since our manifest is called "init.pp" and we are testing for the existance of a class, then by following <a href="https://github.com/rodjek/rspec-puppet#naming-conventions">rspec-puppet's naming convention</a>, we need to name and place our rspec test....:


<pre>
[sher@puppetmaster demo_module]$ tree
.
├── Gemfile
├── manifests
│     └── init.pp
├── metadata.json
├── Rakefile
├── README.md
├── spec
│     ├── classes
│     │     └── init_spec.rb     # Here
│     ├── defines
│     ├── functions
│     ├── hosts
│     └── spec_helper.rb
└── tests
    └── init.pp

7 directories, 8 files
[sher@puppetmaster demo_module]$

</pre>


We have now added the following test:


<pre>
[root@puppetmaster demo_module]# cat spec/classes/init_spec.rb
require 'spec_helper'       
describe 'demo_module' do

  context 'The catalog should at the very least compile' do
    it { 
         should compile
    }
  end

  context 'The main class should be present in the catalog' do
    it { 
         should contain_class('demo_module') 
    }
  end
 
end
[root@puppetmaster demo_module]# 


</pre>

For the purpose of this demo, lets add the following "_xxx" typo error in our init.pp file:

<pre>
[root@puppetmaster demo_module]# cat manifests/init.pp
class demo_module_xxx {

}
[root@puppetmaster demo_module]# 

</pre>


now we run the test:


<pre>

[root@puppetmaster demo_module]# bundle exec rake spec
/usr/local/rvm/rubies/ruby-1.9.3-p551/bin/ruby -S rspec spec/classes/init_spec.rb --color
FF

Failures:

  1) demo_module The catalog should at the very least compile 
     Failure/Error: should compile
     Puppet::Error:
       Could not find class demo_module for puppetmaster.ordsvy.gov.uk on node puppetmaster.ordsvy.gov.uk
     # ./spec/classes/init_spec.rb:6:in `block (3 levels) in <top (required)>'

  2) demo_module The main class should be present in the catalog 
     Failure/Error: should contain_class('demo_module')
     Puppet::Error:
       Could not find class demo_module for puppetmaster.ordsvy.gov.uk on node puppetmaster.ordsvy.gov.uk
     # ./spec/classes/init_spec.rb:12:in `block (3 levels) in <top (required)>'

Finished in 0.07075 seconds
2 examples, 2 failures

Failed examples:

rspec ./spec/classes/init_spec.rb:5 # demo_module The catalog should at the very least compile 
rspec ./spec/classes/init_spec.rb:11 # demo_module The main class should be present in the catalog 
/usr/local/rvm/rubies/ruby-1.9.3-p551/bin/ruby -S rspec spec/classes/init_spec.rb --color failed
[root@puppetmaster demo_module]# 

</pre>

As you can we the puppet master can't even compile the catalog let alone test whether the main class is included in the catalog. That's because we introduced a fundamental error, which is that the main class name is not mirroring the modules name. If we remove the typo and rerun the test, we get:


<pre>

[root@puppetmaster demo_module]# bundle exec rake spec
/usr/local/rvm/rubies/ruby-1.9.3-p551/bin/ruby -S rspec spec/classes/init_spec.rb --color
..

Finished in 0.07467 seconds
2 examples, 0 failures
[root@puppetmaster demo_module]# 

</pre>



<h3>Test 03: Check presence of a subclass</h3>

Let's now say we want to create a new class called "subclass" which should also be present in the class. Before we write this new class, let's write the rspec test for it first, and confirm that it fails.

hence we create the new subclass_rspec.rb rspec testsript:
<pre>
[vagrant@puppetmaster demo_module]$ touch spec/classes/subclass_spec.rb
[vagrant@puppetmaster demo_module]$ tree
.
├── Gemfile
├── Gemfile.lock
├── manifests
│   └── init.pp
├── Modulefile
├── Rakefile
├── README
├── spec
│   ├── classes
│   │   ├── init_spec.rb
│   │   └── subclass_spec.rb         # Here's the new test script
│   ├── defines
│   ├── fixtures
│   │   ├── manifests
│   │   └── modules
│   ├── functions
│   ├── hosts
│   └── spec_helper.rb
└── tests
    └── init.pp
10 directories, 11 files
[vagrant@puppetmaster demo_module]$
</pre>

 

Where the content of the subclass_spec.rb script is:

 
<pre>[vagrant@puppetmaster demo_module]$ cat spec/classes/subclass_spec.rb
require 'spec_helper' 
describe 'demo_module::subclass' do                                    # notice we always use the fqdn
  context 'The following subclass should be in the catalog' do
    it {
         should contain_class('demo_module::subclass')                 # notice we always use the fqdn
    }
  end
end
[vagrant@puppetmaster demo_module]$
</pre>
 

Now if we re-run the test:

 
<pre>[vagrant@puppetmaster demo_module]$ bundle exec rake spec
/usr/local/rvm/rubies/ruby-1.9.3-p551/bin/ruby -S rspec spec/classes/init_spec.rb spec/classes/subclass_spec.rb --color
.F
 
Failures:
 
  1) demo_module::subclass The following subclass should be in the catalog
     Failure/Error: should contain_class('demo_module::subclass')
     Puppet::Error:
       Could not find class demo_module::subclass for puppetmaster.ordsvy.gov.uk on node puppetmaster.ordsvy.gov.uk
     # ./spec/classes/subclass_spec.rb:6:in `block (3 levels) in <top (required)>'
 
Finished in 0.0687 seconds
2 examples, 1 failure
 
Failed examples:
 
rspec ./spec/classes/subclass_spec.rb:5 # demo_module::subclass The following subclass should be in the catalog
/usr/local/rvm/rubies/ruby-1.9.3-p551/bin/ruby -S rspec spec/classes/init_spec.rb spec/classes/subclass_spec.rb --color failed
[vagrant@puppetmaster demo_module]$
</pre>
 


As you can see above, as expected the test has failed, since we haven't created the class yet. Let's create the missing class/manifest now:

 
<pre>[vagrant@puppetmaster demo_module]$ touch manifests/subclass.pp
[vagrant@puppetmaster demo_module]$ tree
.
├── Gemfile
├── Gemfile.lock
├── manifests
│   ├── init.pp
│   └── subclass.pp                # Here we have created the new file.
├── Modulefile
├── Rakefile
├── README
├── spec
│   ├── classes
│   │   ├── init_spec.rb
│   │   └── subclass_spec.rb
│   ├── defines
│   ├── fixtures
│   │   ├── manifests
│   │   └── modules
│   ├── functions
│   ├── hosts
│   └── spec_helper.rb
└── tests
    └── init.pp
10 directories, 12 files
[vagrant@puppetmaster demo_module]$</pre>

 

Now let's add the following content to the subclass.pp file:

 
<pre>[vagrant@puppetmaster demo_module]$ cat manifests/subclass.pp
class demo_module::subclass {
 
}
[vagrant@puppetmaster demo_module]$</pre>

 

As you can this is another empty shell of a class. However this test should still pass because at the moment our tests are simply checking the inclusion of the class names only. so if we now do run the tests, we get:

 
<pre>
[vagrant@puppetmaster demo_module]$ bundle exec rake spec
/usr/local/rvm/rubies/ruby-1.9.3-p551/bin/ruby -S rspec spec/classes/init_spec.rb spec/classes/subclass_spec.rb --color
..
Finished in 0.07104 seconds
2 examples, 0 failures
[vagrant@puppetmaster demo_module]$
</pre>
 



<h3>Test 03 - Check for class parameter values</h3>


Let's now see how we can pass in some class parameter values into a class. We'll use the "file" resource as an example. so in our rspec test we include:

 
<pre>[root@puppetmaster demo_module]# cat spec/classes/init_spec.rb
require 'spec_helper'      
describe 'demo_module' do
  context 'The catalog should at the very least compile' do
    it {
         should compile
    }
  end
  context 'The catalog should contain exactly 2 resources' do
    it {
         should have_resource_count(2)
    }
  end
  context 'The files name should be @/tmp/testfile.txt@ and the files content should be @hello and goodbye@' do
    let(:params){
      {
        :filename    => '/tmp/testfile.txt',
        :filecontent => 'hello and goodbye'
      }
    }
    it {
         should contain_file('/tmp/testfile.txt').with({
           'content' => 'hello and goodbye',
         })
    }
  end
  
end
[root@puppetmaster demo_module]#</pre>

 

Here we have introduced the "let" statement. In software testing terminology, this let statement can be thought of as a "test driver". We also used rspec's ":params" setting to set and pass in the class parameter values. 

 
<pre>[root@puppetmaster demo_module]# bundle exec rake spec
/usr/local/rvm/rubies/ruby-1.9.3-p551/bin/ruby -S rspec spec/classes/init_spec.rb --color
..F
 
Failures:
 
  1) demo_module The files name should be /tmp/testfile.txt and the files content should be goodbye should contain File[/tmp/testfile.txt] with content => "hello and goodbye"
     Failure/Error: })
       expected that the catalogue would contain File[/tmp/testfile.txt] with content set to "hello and goodbye" but it is set to "hello world"
     # ./spec/classes/init_spec.rb:24:in `block (3 levels) in <top (required)>'
 
Finished in 0.32012 seconds
3 examples, 1 failure
 
Failed examples:
 
rspec ./spec/classes/init_spec.rb:21 # demo_module The files name should be /tmp/testfile.txt and the files content should be goodbye should contain File[/tmp/testfile.txt] with content => "hello and goodbye"
/usr/local/rvm/rubies/ruby-1.9.3-p551/bin/ruby -S rspec spec/classes/init_spec.rb --color failed
[root@puppetmaster demo_module]#</pre>

The error message quite easy to understand.

Now let's rewrite our class so that it can now accept the necessary class parameters:

 
<pre>[root@puppetmaster demo_module]# cat manifests/init.pp
class demo_module ($filename = "/tmp/testfile.txt", $filecontent = "hello world") {
  user {'homer':
  }
  file {$filename:
    content => "$filecontent",
  }
}
[root@puppetmaster demo_module]#</pre>

 

Note, we also defined default values for the class parameters. This isn't strictly necessary, however if for some reason the "test drivers" fails to pass in the data to the puppetmaster, then the puppetmaster would still be able to compile the class and give a more meaningful error message, rather than the  highlevel "could not compile" error message. 

 

Now if we re-run the test it passes:

 
<pre>
[root@puppetmaster demo_module]# bundle exec rake spec
/usr/local/rvm/rubies/ruby-1.9.3-p551/bin/ruby -S rspec spec/classes/init_spec.rb --color
...
Finished in 0.34118 seconds
3 examples, 0 failures
[root@puppetmaster demo_module]#
</pre>
 









<h3>Test 05: Check presence of a resource</h3>

So far we have only included empty classes, and then tested that those classes are present in the catalog. Now we are going to go the next level down, and start writing rspec tests that checks for the inclusion of the resources.

In our example, let's say that we want a new file resource, /tmp/testfile.txt as part of the main class. In that case let's first write the test:

 
<pre>
[vagrant@puppetmaster demo_module]$ cat spec/classes/init_spec.rb
require 'spec_helper'      
describe 'demo_module' do
  context 'The following classes should be present in the catalog' do
    it {
         should contain_class('demo_module')                 
    }
  end
 
  context 'the file /tmp/testfile.txt should exist' do       # Here is the code block to test for the existance of the new file resource.
    it {
         should contain_file('/tmp/testfile.txt')        
    }
  end
  
end
[vagrant@puppetmaster demo_module]$
</pre>

 

Now lets run the test:

 
<pre>
[vagrant@puppetmaster demo_module]$ bundle exec rake spec
/usr/local/rvm/rubies/ruby-1.9.3-p551/bin/ruby -S rspec spec/classes/init_spec.rb spec/classes/subclass_spec.rb --color
.F.
 
Failures:
 
  1) demo_module the file /tmp/testfile.txt should exist should contain File[/tmp/testfile.txt]
     Failure/Error: should contain_file('/tmp/testfile.txt')
       expected that the catalogue would contain File[/tmp/testfile.txt]
     # ./spec/classes/init_spec.rb:11:in `block (3 levels) in <top (required)>'
 
Finished in 0.15251 seconds
3 examples, 1 failure
 
Failed examples:
 
rspec ./spec/classes/init_spec.rb:10 # demo_module the file /tmp/testfile.txt should exist should contain File[/tmp/testfile.txt]
/usr/local/rvm/rubies/ruby-1.9.3-p551/bin/ruby -S rspec spec/classes/init_spec.rb spec/classes/subclass_spec.rb --color failed
[vagrant@puppetmaster demo_module]$
</pre>
 

This failed, as expected. Now let's add the resource to the manifest:

 
<pre>
[vagrant@puppetmaster demo_module]$ cat manifests/init.pp
class demo_module {
  file {'/tmp/testfile.txt':
  }
}
[vagrant@puppetmaster demo_module]$
</pre>

 

Notice, that for the time being we declared a file resource but without any attributes whatsoever, we will look at attributes later on. 

 

Now let's rerun the test:

 
<pre>
[vagrant@puppetmaster demo_module]$ bundle exec rake spec
/usr/local/rvm/rubies/ruby-1.9.3-p551/bin/ruby -S rspec spec/classes/init_spec.rb spec/classes/subclass_spec.rb --color
...
Finished in 0.14835 seconds
3 examples, 0 failures
[vagrant@puppetmaster demo_module]$ cat manifests/init.pp
class demo_module {
  file {'/tmp/testfile.txt':
  }
}
</pre>

 

Now, let's see what happens when we add another file resource to the main class:

 
<pre>
[vagrant@puppetmaster demo_module]$ cat manifests/init.pp
class demo_module {
  file {'/tmp/testfile.txt':
  }
  file {'/tmp/anothertestfile.txt':
  }
}
[vagrant@puppetmaster demo_module]$
</pre>
 









==================================================================







Nesting manifests

All manifests are stored under the manifests folders. However if your modules has a lot of manifests then you may want to create your own folder structure beneath the manifests folder and organise your manifests into these folders. If you do this then your rspec tests will fail because it won't be able to locate the manifests, hence you then need to update the rspec tests. Let's say we want to move the subclass.pp manifest to a new folder called "other", then in the spirit of TDD/BDD, let's update the rspec code first:

 
[vagrant@puppetmaster demo_module]$ cat spec/classes/subclass_spec.rb
require 'spec_helper' 
describe 'demo_module::other::subclass' do                        # notice the fqdn now contains "other"
  context 'The following subclass should be in the catalog' do
    it {
         should contain_class('demo_module::other::subclass')     # notice the fqdn now contains "other"
    }
  end
end
[vagrant@puppetmaster demo_module]$

 

Now let's run the test:

 
[vagrant@puppetmaster demo_module]$ bundle exec rake spec
/usr/local/rvm/rubies/ruby-1.9.3-p551/bin/ruby -S rspec spec/classes/init_spec.rb spec/classes/subclass_spec.rb --color
.F
 
Failures:
 
  1) demo_module::other::subclass The following subclass should be in the catalog
     Failure/Error: should contain_class('demo_module::other::subclass')     # notice the fqdn now contains "other"
     Puppet::Error:
       Could not find class demo_module::other::subclass for puppetmaster.ordsvy.gov.uk on node puppetmaster.ordsvy.gov.uk
     # ./spec/classes/subclass_spec.rb:5:in `block (3 levels) in <top (required)>'
 
Finished in 0.06494 seconds
2 examples, 1 failure
 
Failed examples:
 
rspec ./spec/classes/subclass_spec.rb:4 # demo_module::other::subclass The following subclass should be in the catalog
/usr/local/rvm/rubies/ruby-1.9.3-p551/bin/ruby -S rspec spec/classes/init_spec.rb spec/classes/subclass_spec.rb --color failed
[vagrant@puppetmaster demo_module]$

 

This time it has failed as expected. Now let's create the "other" folder and move the subclass.pp into it:

 
[vagrant@puppetmaster demo_module]$ mkdir manifests/other
[vagrant@puppetmaster demo_module]$ mv manifests/subclass.pp manifests/other/subclass.pp
[vagrant@puppetmaster demo_module]$ tree
.
├── Gemfile
├── Gemfile.lock
├── manifests
│   ├── init.pp
│   └── other                  # have now created this folder.
│       └── subclass.pp        # have now place this manifest into here.
├── Modulefile
├── Rakefile
├── README
├── spec
│   ├── classes
│   │   ├── init_spec.rb
│   │   └── subclass_spec.rb
│   ├── defines
│   ├── fixtures
│   │   ├── manifests
│   │   └── modules
│   ├── functions
│   ├── hosts
│   └── spec_helper.rb
└── tests
    └── init.pp
11 directories, 12 files
[vagrant@puppetmaster demo_module]$

 

We also have to update the fqdn in the subclass.pp manifest to include "other" too:

 
[vagrant@puppetmaster demo_module]$ cat manifests/other/subclass.pp
class demo_module::other::subclass {                                
}

 

Now lets rerun the test:

 
[vagrant@puppetmaster demo_module]$ bundle exec rake spec
/usr/local/rvm/rubies/ruby-1.9.3-p551/bin/ruby -S rspec spec/classes/init_spec.rb spec/classes/subclass_spec.rb --color
..
Finished in 0.06801 seconds
2 examples, 0 failures
[vagrant@puppetmaster demo_module]$

 

Now it has passed.
Testing for the existance of a resource

So far we have only included empty classes, and then tested that those classes are present in the catalog. Let's  now test introduce a new "user" resource into one of our class and tests that it exists. So let's go ahead




<h3>Test 06: Check the number of resources </h3>

We want to test that the catalog contains x number of resources. In our case, let's say 3 resources. 






<pre>
[root@puppetmaster demo_module]# cat spec/classes/init_spec.rb
require 'spec_helper'      
describe 'demo_module' do
  context 'The catalog should at the very least compile' do
    it {
         should compile
    }
  end
  context 'The catalog should contain exactly 2 resources' do
    it {
         should have_resource_count(2)
    }
  end
end
[root@puppetmaster demo_module]#
</pre>














See also:

https://relishapp.com/rspec/docs/gettingstarted