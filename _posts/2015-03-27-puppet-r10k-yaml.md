---
ID: 333
post_title: 'Puppet &#8211; r10k.yaml'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-r10k-yaml
published: true
post_date: 2015-03-27 00:00:00
---
https://docs.puppetlabs.com/pe/latest/r10k_yaml.html

r10k comes with it's own config file which is usually in:



<pre>
$ cd /etc/puppet              # note you have to cd into this folder first. 
$ cat /etc/puppet/r10k.yaml.         # note, you have to create this file if it doesn't already exist. 
:cachedir: '/var/cache/r10k'

:sources:
 puppet:
    remote: http://{github url that contains puppetfile}.git
    basedir: '/etc/puppet/environments'    
    prefix: false #This defaults to false
 hiera:
    remote: http://{github url that contains yaml files}/hiera.git     
    basedir: '/etc/puppet/hiera'
    prefix: false

</pre>

The puppet repo above needs to have this structure:

<pre>
[root@puppetmaster platform_provision]# tree
.
├── environment.conf
├── Gemfile
├── manifests
│     └── site.pp
├── modules
└── Puppetfile      # important. 
</pre>

Where the Gemfile contains:

<pre>
$ cat Gemfile
source 'https://rubygems.org'
</pre>

And environment.conf contains:

<pre>
$ cat environment.conf
manifest  = manifests/site.pp
moduledir = modules
</pre>

As for the hiera repo, you simply place your .yaml datasource in a folder structure to your liking.  

Note: the site.pp file here can be left empty if you are using an enc like foreman. Alternatively you could have a "default-node" definition to include class that you want installed in all vms. 


This config file is used by r10k when you run the following command:


<pre>
$ r10k deploy environment -p {environmentName} --verbose
</pre>

Where "{environmentName}" should be set to <code>prefix_branchname</code>. The prefix is something you can choose, e.g. you can name it the application you are working on. 

Now let's say you have a project, called "projectA", and this project requires several seperate directory-environments (e.g. called dev,test,prod...etc), in which case you might want r10k to create the following directory-environments:


<pre>
$ ls -l /etc/puppet/environments/
total 44
drwxr-xr-x 5 root root 4096 Dec 11 12:47 projectA_dev
drwxr-xr-x 5 root root 4096 Apr 15 16:03 projectA_test
drwxr-xr-x 5 root root 4096 Apr 24 13:40 projectA_prod
</pre>

As well as the corresponding hiera data sources:

<pre>
$ ls -l /etc/puppet/hiera/
drwxr-xr-x 5 root root 4096 Dec 11 12:47 projectA_dev
drwxr-xr-x 5 root root 4096 Apr 15 16:03 projectA_test
drwxr-xr-x 5 root root 4096 Apr 24 13:40 projectA_prod
</pre>

In order to achieve the above scenario, all you need to do is to achieve this, is to ensure that git branches by the names "dev", "test", and "prod" exists. 

Then during an r10k run, r10k will create a folder from each repo branch. then it will populate the modules folder with each branches puppetfile. 


Now we have limitation with this approach. What if projectB comes along, which also has "dev", "test", and "prod" environments, which needs their own directory environments. You can achieve this by adding multiple entres in your r10k.yaml file. 


<pre>
$  cat /etc/r10k.yaml
:cachedir: '/var/cache/r10k'

:sources:
 puppet_ProjectA:
    remote: http://{github url that contains puppetfile}.git
    basedir: '/etc/puppet/environments'
    prefix: 'ProjectA'
 hiera_ProjectA:
    remote: http://{github url that contains yaml files}.git
    basedir: '/etc/puppet/hiera'
    prefix: 'ProjectA'
 puppet_ProjectB:
    remote: http://{github url that contains puppetfile}.git
    basedir: '/etc/puppet/environments'
    prefix: 'ProjectB'
 hiera_ProjectB:
    remote: http://{github url that contains yaml files}.git
    basedir: '/etc/puppet/hiera'
    prefix: 'ProjectB'

:purgedirs:
  - /etc/puppet/environments
  - /etc/puppet/hiera

</pre>


Notice here we used the "prefix" setting. thats because if we didn't then r10k will create a folder called "dev" in the environment folder twice, which could cause errors.  

<pre>
$ ls -l /etc/puppet/environments/
total 44
drwxr-xr-x 5 root root 4096 Dec 11 12:47 ProjectA_dev
drwxr-xr-x 5 root root 4096 Apr 15 16:03 ProjectA_test
drwxr-xr-x 5 root root 4096 Apr 24 13:40 ProjectA_prod
drwxr-xr-x 5 root root 4096 Dec 11 12:47 ProjectB_dev
drwxr-xr-x 5 root root 4096 Apr 15 16:03 ProjectB_test
drwxr-xr-x 5 root root 4096 Apr 24 13:40 ProjectB_prod
</pre>

Now you need update all your puppet agent's puppet.conf file with an environment setting, e.g.:


<pre>
[root@puppetagent1 ~]# cat /etc/puppet/puppet.conf | grep env
environment     = ProjectB_dev
[root@puppetagent2 ~]# cat /etc/puppet/puppet.conf | grep env
environment     = ProjectA_prod
</pre>

That way the puppetmaster will know which directory environment to use to generate the catalog.