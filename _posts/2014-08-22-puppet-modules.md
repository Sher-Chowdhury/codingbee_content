---
ID: 144
post_title: 'Puppet &#8211; Modules'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-modules
published: true
post_date: 2014-08-22 00:00:00
---
In the previous tutorial, we saw that a module is essentially folder, where it's structure and contents follows a set of rules. If you follow all these rules, then puppet will "autoload" the module, and hence automtically make the class residing in the init.pp file already available to be called from the site.pp file. 

We only created a barebone module, but a proper module can have other folders and files as you add more features to your module. (for example called "ntp") has the following following structure: 
<pre>
/etc/puppet/module/        # all modules are stored in this directory (which is called the <a href="https://docs.puppetlabs.com/learning/modules1.html#the-modulepath">modulepath</a>)
                |
                +-ntp/     # Rule: the module's main folder must be named after the module itself.  
                   |
                   +-files/      # This contains a bunch of static filee, which can be downloaded by puppet agents.
                   --templates/  # This contains templates that are used by a module’s manifest.
                   --lib/        # This contains plugins like custom facts and custom resource types.
                   --tests/      # This contains examples showing how to declare the module’s classes and defined types.
                   --spec/       # This contains spec tests for any plugins in the lib folder.
                   --manifests/  # this houses all your manifests. You can have any number of manifest files.  
                        |
                        +- init.pp   # rule: all modules should have a manifest called init.pp
                             |
                             +- class ntp   # rule: all init.pp must have only one class, and this class 
                                                 # must be named after the module itself. 
                        +- manifest1.pp
                        +- manifest2.pp
                        +- manifest3.pp  # ....and etc. You need to follow some extra conventions 
                                             # when dealing with other manifests. Cover below. 

 
 
</pre>



Therefore to follow this structure in our "user_account" example, we need to start by creating the other folders:

<pre>
[root@puppetmaster user_account]# pwd
/etc/puppet/modules/user_account
[root@puppetmaster user_account]# ls
manifests
[root@puppetmaster user_account]# mkdir files templates lib spec
[root@puppetmaster user_account]# cd ..
[root@puppetmaster modules]# pwd
/etc/puppet/modules
[root@puppetmaster modules]# tree .
.
└── user_account
    ├── files
    ├── lib
    ├── manifests
    │     └── init.pp
    ├── spec
    └── templates

6 directories, 1 file
[root@puppetmaster modules]#
</pre> 

We will cover about what goes into the other folders later. 

However for now the key thing to remember that several rules/conventions needs to be followed in order to avoid confusion and for puppet to properly autoload the module:

<ul>
	<li>Rule - The init.pp must contain a single class definition, the class's name must be the same as the module's name. </li>

	<li>Best Practice - Each manifest in a module should contain only one class.</li>
	<li>Rule - aside from the init.pp manifest, which is special, all other manifests in the module must be named after the class's name that it contains. E.g. a manifest called "group_account.pp" must contain a class called "group_account"</li>
	<li>rule - when calling another class in the module, i.e. not the one in init.pp, then you need to use the class's FQDN. </li>
	<li>Rule - Modules have to be stored in one of the approved directories, as specified by Puppet's "modulepath" setting. You can view the approved direcotories by using "puppet config print modulepath" or a better yet, get the <a href="https://docs.puppetlabs.com/learning/modules1.html#aside-configprint">setting the puppet master will use</a>. </li>

</ul>


When creating a new module, you have to create all the folders/files from scratch, and then leave the folders as placeholders for later use, if you need them. It can be a bit tedious/error-prone to create all these folders manually. Luckily Puppet can generate this for you using the following the <code>puppet module generate username-modulename</code> command. This command will prompt you for some information (in order to create the metadata.json file) in which case you can accept the defaults for now:

<pre>
[root@puppetmaster modules]# puppet module generate codingbee-dummymodule

We need to create a metadata.json file for this module.  Please answer the
following questions; if the question is not applicable to this module, feel free
to leave it blank.

Puppet uses Semantic Versioning (semver.org) to version modules.
What version is this module?  [0.1.0]
--> 

Who wrote this module?  [codingbee]
--> 

What license does this module code fall under?  [Apache 2.0]
--> 

How would you describe this module in a single sentence?
--> 

Where is this module's source code repository?
--> 

Where can others go to learn more about this module?
--> 

Where can others go to file issues about this module?
--> 

----------------------------------------
{
  "name": "codingbee-dummymodule",
  "version": "0.1.0",
  "author": "codingbee",
  "summary": null,
  "license": "Apache 2.0",
  "source": "",
  "project_page": null,
  "issues_url": null,
  "dependencies": [
    {"name":"puppetlabs-stdlib","version_requirement":">= 1.0.0"}
  ]
}
----------------------------------------

About to generate this metadata; continue? [n/Y]
--> y

Notice: Generating module at /etc/puppet/modules/codingbee-dummymodule...
Notice: Populating templates...
Finished; module generated in codingbee-dummymodule.
codingbee-dummymodule/tests
codingbee-dummymodule/tests/init.pp
codingbee-dummymodule/spec
codingbee-dummymodule/spec/spec_helper.rb
codingbee-dummymodule/spec/classes
codingbee-dummymodule/spec/classes/init_spec.rb
codingbee-dummymodule/README.md
codingbee-dummymodule/Rakefile
codingbee-dummymodule/manifests
codingbee-dummymodule/manifests/init.pp
codingbee-dummymodule/metadata.json
codingbee-dummymodule/Gemfile
[root@puppetmaster modules]#

</pre>

Note: you have to run this command while in the module's folder. 
Note: you have to pass the module's name as a "authorname-modulename" construct. This is so that you can ensure your module has a unique name if you decide to share your module on <a href="https://forge.puppetlabs.com/">puppetforge</a>. Hence your puppetforge account's username is what you start your module's name with. 

After that it creates the following:



<pre>
[root@puppetmaster modules]# cd codingbee-dummymodule/
[root@puppetmaster codingbee-dummymodule]# tree -a
.
├── Gemfile
├── manifests
│     └── init.pp
├── metadata.json
├── Rakefile
├── README.md
├── spec
│     ├── classes
│     │     └── init_spec.rb
│     └── spec_helper.rb
└── tests
    └── init.pp

4 directories, 8 files
[root@puppetmaster codingbee-dummymodule]# 
</pre>






<strong>See Also:</strong>
https://docs.puppetlabs.com/learning/modules1.html

https://docs.puppetlabs.com/puppet/latest/reference/lang_namespaces.html

https://docs.puppetlabs.com/learning/modules1.html



<a href="http://docs.puppetlabs.com/puppet/latest/reference/modules_fundamentals.html">Module Fundamentals</a>

http://puppetlabs.com/blog/best-practices-building-puppet-modules