---
ID: 244
post_title: 'Puppet &#8211; Modules (the &#8220;files&#8221; folder)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-modules-the-files-folder
published: true
post_date: 2014-12-25 00:00:00
---
One thing you may want to do (for a given class), is distribute a particular static file to your agent.

This can easily be done using modules. Let's take the previous "user_account" example, and say we want to distribute a file called "user.txt" to the /tmp folder of the agents. Let's say that the content of this file is "hello world".

To do this, we first create user.txt and place it in the user_account module's files folder:

<pre>
[root@puppetmaster files]# pwd
/etc/puppet/modules/user_account/files
[root@puppetmaster files]# echo "Hello world" > user-master-copy.txt
[root@puppetmaster files]# ls
user-master-copy.txt
</pre>

Notice, that we have named the file "user-master-copy.txt" rather than user.txt. This is to give you a better view of what's happening. This file will be copied across and then renamed to user.txt by the resource declaration, which we'll do next. 


Now to distribute this file, we have to define a <a href="https://docs.puppetlabs.com/references/latest/type.html#file">file resource</a> to do this:

<pre>
file { '/tmp/user.txt':
  ensure  => file,
  source  => "puppet:///modules/user_account/user-master-copy.txt",
}
</pre>

The "puppet:///modules/{module-name}/.../{filename}" syntax is the way we tell puppet that the source file is located in the "files" folder in the user_account module, and it is called "user-master-copy.txt". Note, the "..." means that under the files folder, you can create your own custom folder structure in order to organise all your source files. In which you need to provide puppet with the relative path. 

Now let's way we want to apply this as part of the user_account class, in that case we can insert this resource declaration into this class:

<pre>
[root@puppetmaster manifests]# pwd
/etc/puppet/modules/user_account/manifests
[root@puppetmaster manifests]# cat init.pp
class user_account ($username = 'homer'){

  user { $username:
    ensure => present,
    uid    => '101',
    shell  => '/bin/bash',
    home   => "/home/$username",
  }

  file { '/tmp/user.txt':
    ensure  => file,
    source  => "puppet:///modules/user_account/user-master-copy.txt",
  }


}
[root@puppetmaster manifests]#

</pre>
Note, we don't need to make any changes to the site.pp file since it already calls the user_account, and the changes we have made are internal to this class. 

Now if you we do a puppet run on the agent, we get:

<pre>

[root@puppetagent1 tmp]# pwd
/tmp
[root@puppetagent1 tmp]# ls -l user.txt
ls: cannot access user.txt: No such file or directory
[root@puppetagent1 tmp]# puppet agent --test
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Caching catalog for puppetagent1.codingbee.dyndns.org
Info: Applying configuration version '1419488509'
Notice: /Stage[main]/User_account/File[/tmp/user.txt]/ensure: defined content as '{md5}f0ef7081e1539ac00ef5b761b4fb01b3'
Notice: Finished catalog run in 0.24 seconds
[root@puppetagent1 tmp]# ls -l user.txt
-rw-r--r--. 1 root root 12 Dec 25 06:32 user.txt
[root@puppetagent1 tmp]#

</pre>

Now what if we want to create a lower level tree structure inside the files folder, to further organise our source files, e.g. lets say we create a new samples folder, and place our file into it:

<pre>
[root@puppetmaster files]# pwd
/etc/puppet/modules/user_account/files
[root@puppetmaster files]# ls
user-master-copy.txt
[root@puppetmaster files]# mkdir samples
[root@puppetmaster files]# ls -l
total 8
drwxr-xr-x. 2 root root 4096 Dec 25 06:44 samples
-rw-r--r--. 1 root root   12 Dec 25 06:05 user-master-copy.txt
[root@puppetmaster files]# mv user-master-copy.txt samples/
[root@puppetmaster files]# cd samples/
[root@puppetmaster samples]# ls
user-master-copy.txt
</pre>

Now if we do a puppet run:

<pre>

[root@puppetagent1 tmp]# puppet agent --test
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Caching catalog for puppetagent1.codingbee.dyndns.org
Info: Applying configuration version '1419488509'
Error: /Stage[main]/User_account/File[/tmp/user.txt]: Could not evaluate: Could not retrieve information from environment production source(s) puppet:///modules/user_account/user-master-copy.txt


</pre>

This failed because we need to update our file-resource's source attribute to specify the source file's new (relative) location:

<pre>
[root@puppetmaster manifests]# cat init.pp
class user_account ($username = 'homer'){

  user { $username:
    ensure => present,
    uid    => '101',
    shell  => '/bin/bash',
    home   => "/home/$username",
  }

  file { '/tmp/user.txt':
    ensure  => file,
    source  => "puppet:///modules/user_account/samples/user-master-copy.txt",
  }


}

</pre>

Now when we do a puppet run it works:


<pre>

[root@puppetagent1 tmp]# puppet agent --test
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Caching catalog for puppetagent1.codingbee.dyndns.org
Info: Applying configuration version '1419490479'
Notice: /Stage[main]/User_account/File[/tmp/user.txt]/ensure: defined content as '{md5}f0ef7081e1539ac00ef5b761b4fb01b3'
Notice: Finished catalog run in 0.19 seconds
[root@puppetagent1 tmp]# cat user.txt
Hello world
[root@puppetagent1 tmp]#


</pre>