---
ID: 135
post_title: 'Puppet &#8211; Manifests'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-manifests
published: true
post_date: 2014-08-08 00:00:00
---
In Puppet, it is intended that resources are written into files using the resource declaration syntax. In Puppet these files are referred to as "Manifests" and they must end with the ".pp" extension.

&nbsp;

For example let's say we have a couple of resources declared in a manifest file called "CatAndMouse.pp":

<pre>
[root@puppetmaster ~]# pwd
/root
[root@puppetmaster ~]# cat CatAndMouse.pp
user { 'Tom':
   ensure => 'present',
   home     => '/home/Tom',
   shell   => '/bin/bash',
   uid       => '101',
}

user { 'Jerry':
   ensure => 'present',
   home     => '/home/Jerry',
   shell   => '/bin/bash',
   uid       => '102',
}
[root@puppetmaster ~]#
</pre>

Note: The resource's title and the attribute names should always be lowercase.

&nbsp;

We are now going to load this file so that users Tom &amp; Jerry exists on the local machine (where they currently don't exist). To load these into puppet we use the "apply" subcommand, like this:

<pre>
[root@puppetmaster examples]# puppet apply /root/examples/CatAndMouse.pp
Notice: Compiled catalog for puppetmaster.codingbee.dyndns.org in environment production in 0.12 seconds
Notice: /Stage[main]/Main/User[Tom]/ensure: created
Notice: /Stage[main]/Main/User[Jerry]/ensure: created
Notice: Finished catalog run in 0.14 seconds
[root@puppetmaster examples]#
</pre>

Users Tom &amp; Jerry now exists.

&nbsp;

Now if we tried to apply the same manifest again:

<pre>
[root@puppetmaster examples]# puppet apply /root/examples/CatAndMouse.pp
Notice: Compiled catalog for puppetmaster.codingbee.dyndns.org in environment production in 0.12 seconds
Notice: Finished catalog run in 0.04 seconds
[root@puppetmaster examples]#
</pre>

This time nothing happened, that's because the machine is already at it's desired state, i.e. both users exist.

There's a few things you need to remember about puppet:
<ul>
	<li>It only Enforces attributes that are explicitly specified. When we omit attributes, Puppet doesn’t manage them, as a result these attributes take on whatever value that linux initially assigns to it, or whatever value you assign to it. You can change these values and Puppet will not change it</li>
	<li>Each resource type has an attribute whose value is automatically set to match the resource's title. This type of attribute is referred to as "namevars" and they are a little special because Puppet will actively enforce this even if they are not explicitly specified. For that reason, a namevars attribute's value is unique and can be used to identify a resource. Note, a very small number of resource types don't have a namevars attribute.</li>
	<li>If a user makes a change that results in drift, then puppet will undo those changes on the next run, which by default should be within 30 mins.</li>
	<li>Most resource types have an attribute called "ensure", this attribute controls whether a resource exists or not, and if it does exist, then in what form.</li>
</ul>
&nbsp;

Here's another example, but this time we have a manifest called (DummyFile.pp) and it has a resource to create a dummy file:

<pre>
[root@puppetmaster ~]# cat /root/examples/DummyFile.pp
file {'testfile.txt':
               ensure   => present,
               path       => '/tmp/testfile.txt',
               mode       => 0777,
               content => "Hello World \n",
}
notify {"INFO: This is a customised log message":}

[root@puppetmaster examples]# puppet apply DummyFile.pp
Notice: Compiled catalog for puppetmaster.codingbee.dyndns.org in environment production in 0.07 seconds
Notice: INFO This is a custom message
Notice: /Stage[main]/Main/Notify[INFO This is a custom message]/message: defined 'message' as 'INFO This is a custom message'
Notice: Finished catalog run in 0.10 seconds
[root@puppetmaster examples]#
</pre>

Notice here that we used a customised log message using the "notify" statement.
&nbsp;

Now let's confirm that this file now exists:

<pre>
[root@puppetmaster examples]# ls -l /tmp/testfile.txt
-rwxrwxrwx. 1 root root 13 Aug  6 23:20 /tmp/testfile.txt
[root@puppetmaster examples]# cat /tmp/testfile.txt
Hello World
[root@puppetmaster examples]#
</pre>

Now lets try changing the file's permission to 755.:

<pre>
[root@puppetmaster examples]# chmod 755 /tmp/testfile.txt
[root@puppetmaster examples]# ls -l /tmp/testfile.txt
-rwxr-xr-x. 1 root root 13 Aug  6 23:20 /tmp/testfile.txt
[root@puppetmaster examples]#
</pre>

<pre>
[root@puppetmaster examples]# puppet apply DummyFile.pp
Notice: Compiled catalog for puppetmaster.codingbee.dyndns.org in environment production in 0.07 seconds
Notice: /Stage[main]/Main/File[testfile.txt]/mode: mode changed '0755' to '0777'
Notice: INFO This is a custom message
Notice: /Stage[main]/Main/Notify[INFO This is a custom message]/message: defined 'message' as 'INFO This is a custom message'
Notice: Finished catalog run in 0.09 seconds
[root@puppetmaster examples]# ls -l /tmp/testfile.txt
-rwxrwxrwx. 1 root root 13 Aug  6 23:20 /tmp/testfile.txt
[root@puppetmaster examples]#
</pre>

Here you'll notice that puppet has re-enforced the desired permission configuration again. 

In the case of the "file" resource type, the "path" is actually the file resource type's namevar. This means that  the following:

<pre>
[root@puppetmaster ~]# cat /root/examples/DummyFile.pp
file {'/tmp/testfile.txt':
        ensure  => present,
        path    => '/tmp/testfile.txt',
        mode    => 0777,
        content => "Hello World \n",
}
</pre>

Can be simplified by removing the "path" attribute and it's value, i.e. we have:

<pre>
[root@puppetmaster ~]# cat /root/examples/DummyFile.pp
file {'/tmp/testfile.txt':
        ensure  => present,
        mode    => 0777,
        content => "Hello World \n",
}
</pre>

....An the overall result remains the same. 
&nbsp;


In this lesson we applied resource to the local machine. 



Useful links:

https://docs.puppetlabs.com/learning/manifests.html

https://docs.puppetlabs.com/puppet/latest/reference/dirs_manifest.html

&nbsp;