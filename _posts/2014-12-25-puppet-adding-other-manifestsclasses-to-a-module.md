---
ID: 245
post_title: 'Puppet &#8211; Adding other manifests/classes to a module'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-adding-other-manifestsclasses-to-a-module
published: true
post_date: 2014-12-25 00:00:00
---
In the previous tutorial we created a new file resource declaration and inserted it into the main class. But what if we instead want to have this file-resource in it's own standalone class, but still inside the same module. This is can be done by doing the following:

<ol>
	<li>Create a separate manifest and place it inside the manifests folder</li>
	<li>In this manifest write a single class, and insert the resource declaration into it.</li>
	<li>the manifest and the class must share the same name.</li>
	<li>Update the site.pp file to reference the new class, and use "::" to help puppet locate the new class</li>
</ol>

Using our user_account example, let's call our new class "user_tmp_file". In that case, here are the changes we make to the module:

<pre>
[root@puppetmaster modules]# pwd
/etc/puppet/modules
[root@puppetmaster modules]# tree .
.
└── user_account
    ├── files
    │     └── samples
    │         └── user-master-copy.txt
    ├── lib
    ├── manifests
    │     ├── init.pp
    │     └── user_tmp_file.pp
    ├── spec
    └── templates

7 directories, 3 files
[root@puppetmaster modules]# cat user_account/manifests/init.pp
class user_account ($username = 'homer'){

  user { $username:
    ensure => present,
    uid    => '101',
    shell  => '/bin/bash',
    home   => "/home/$username",
  }

}
[root@puppetmaster modules]# cat user_account/manifests/user_tmp_file.pp
class user_account::user_tmp_file {

  file { '/tmp/user.txt':
    ensure  => file,
    source  => "puppet:///modules/user_account/samples/user-master-copy.txt",
  }

}
[root@puppetmaster modules]# cat user_account/files/samples/user-master-copy.txt
Hello world
[root@puppetmaster modules]#

</pre>

Notice: In the above we used "::" for namespacing, i.e. to help with drilling down to a particular class. It is a bit like an FQDN for a class in a module. 



So far so good. Now we reference this new class (along with which module it belongs to) in the site.pp:


<pre>
[root@puppetmaster modules]# cat /etc/puppet/manifests/site.pp

node 'PuppetAgent1' {
  class {user_account:}
  class {user_account::user_tmp_file:}           # notice the "::" namespace syntax
}

node 'PuppetAgent2' {
  class {user_account:
   username => "bart",
  }
}
[root@puppetmaster modules]#
</pre>


Now if we do a puppet run, we get:

<pre>

[root@puppetagent1 tmp]# puppet agent --test
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Caching catalog for puppetagent1.codingbee.dyndns.org
Info: Applying configuration version '1419492288'
Notice: /Stage[main]/User_account::User_tmp_file/File[/tmp/user.txt]/ensure: defined content as '{md5}f0ef7081e1539ac00ef5b761b4fb01b3'
Notice: Finished catalog run in 0.20 seconds
[root@puppetagent1 tmp]# ls -l user.txt
-rw-r--r--. 1 root root 12 Dec 25 07:29 user.txt
[root@puppetagent1 tmp]#
</pre>

In the manifests folder in our module, we can create our custom folder structure to further organize our manifests. In which case we need to update our namespacing to reflect the new structure. For example let's say our new manifest needs to be placed into a new folder called static-files-manifests. In that case we would have:


<pre>
[root@puppetmaster manifests]# pwd
/etc/puppet/modules/user_account/manifests
[root@puppetmaster manifests]# ls
init.pp  user_tmp_file.pp
[root@puppetmaster manifests]# mkdir static-files-manifests
[root@puppetmaster manifests]# mv user_tmp_file.pp static-files-manifests/
[root@puppetmaster manifests]# cd ..
[root@puppetmaster user_account]# cd ..
[root@puppetmaster modules]# tree .
.
└── user_account
    ├── files
    │     └── samples
    │         └── user-master-copy.txt
    ├── lib
    ├── manifests
    │     ├── init.pp
    │     └── static-files-manifests            # this is the new folder
    │         └── user_tmp_file.pp
    ├── spec
    └── templates

8 directories, 3 files

</pre>

Now we update the manifest and site.pp to reflect the new folder structure, using namespaces:

<pre>
[root@puppetmaster static-files-manifests]# cat user_tmp_file.pp
class user_account::static-files-manifests::user_tmp_file {

  file { '/tmp/user.txt':
    ensure  => file,
    source  => "puppet:///modules/user_account/samples/user-master-copy.txt",
  }

}
[root@puppetmaster static-files-manifests]# cat /etc/puppet/manifests/site.pp
# import '/etc/puppet/modules/create_user/manifests/init.pp'


node 'PuppetAgent1' {
  class {user_account:}
  class {user_account::static-files-manifests::user_tmp_file:}
}

node 'PuppetAgent2' {
  class {user_account:
   username => "bart",
  }
}
[root@puppetmaster static-files-manifests]#

</pre>

Notice that we updated the namespacing for both the manifests and the site.pp. We always have to do this to help puppet reference everything without any ambiguity. 

Now if we do a puppet run:

<pre>
[root@puppetagent1 tmp]# ls -l user.txt
ls: cannot access user.txt: No such file or directory
[root@puppetagent1 tmp]# puppet agent --test
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Caching catalog for puppetagent1.codingbee.dyndns.org
Info: Applying configuration version '1419493159'
Notice: /Stage[main]/User_account::Static-files-manifests::User_tmp_file/File[/tmp/user.txt]/ensure: defined content as '{md5}f0ef7081e1539ac00ef5b761b4fb01b3'
Notice: Finished catalog run in 0.20 seconds
[root@puppetagent1 tmp]# ls -l user.txt
-rw-r--r--. 1 root root 12 Dec 25 07:39 user.txt
[root@puppetagent1 tmp]#

</pre>




<strong>See also:</strong>
https://docs.puppetlabs.com/puppet/latest/reference/lang_namespaces.html