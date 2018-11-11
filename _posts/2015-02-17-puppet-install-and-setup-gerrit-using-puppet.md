---
ID: 297
post_title: 'Puppet &#8211; Install and setup Gerrit using Puppet'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-install-and-setup-gerrit-using-puppet
published: true
post_date: 2015-02-17 00:00:00
---
<h2>Create the gerrit user</h2>

This can be done using the following resources:

<pre>
file{'/opt':
    ensure => directory,
  }

#  file{'/opts/gerrit':            # this might not be needed. 
#    ensure => directory,
#  }

user{'gerrit':
    ensure => present,
    home => '/opt/gerrit',
    managehome => true
  }

</pre>



need to also have java jdk installed :

<pre>
  package {'java-1.8.0-openjdk':
    ensure => present,
  }
</pre>


, or use the following puppet resource:

<pre>
  package {'git':
    ensure => present,
  }
}

</pre>





Alternatively this could be done via <strong>puppet</strong> using the file resource:


<pre>
create a file resource, with title=/opt/gerrit/gerrit.war, ensure=present, source=http://gerrit-releases.storage.googleapis.com/gerrit-2.10.war  
</pre>

Note, I replaced "https" with "http" because Puppet struggles to download the file using https. Luckily http also works.   



This pre-prepared gerrit.config can also be used to automate gerrit install via puppet, which we'll cover next:



<h3>Perform the (init) installation</h3>
With the puppet approach, you need to already have the following pre-prepared files:


<pre>
[gerrit@puppetagent02 etc]$ pwd
/opt/gerrit/etc
[gerrit@puppetagent02 etc]$ ls -l
total 16
-rw-rw-r-- 1 gerrit gerrit  611 Feb 11 11:52 gerrit.config       # this one...
drwxrwxr-x 2 gerrit gerrit 4096 Feb 11 11:52 mail
-rw------- 1 gerrit gerrit  215 Feb 11 11:52 secure.config       # ...and this one. 
-rw------- 1 gerrit gerrit 1219 Feb 11 11:52 ssh_host_key       # not sure about this one though. 
[gerrit@puppetagent02 etc]$

</pre>


Best way to get a hold of these files is to do a manual POC (proof of concept) installation and then use the files generated from the POC, for the puppet automation. You can can keep these file hardcoded in the files folder, or convert them into parameterised erb-templates. 

For now let's assumes we will use static files, in which case we need 2 file resources:

<pre>
file resource with title=/opt/etc/gerrit.config, source=puppet:///gerrit/file/gerrit.config
file resource with title=/opt/etc/secure.config, source=puppet:///gerrit/file/secure.config
</pre>
  
Once we have those in place, we can then run the following exec resource:

<pre>
exec resource with command="java -jar gerrit.war init --batch --site-path /opt/gerrit", and user=gerrit, cwd=/opt/gerrit
</pre>


For puppet automation of installing plugins, it's best to do this by creating a parametised defined-type for installing plugin, and this defined-type contains an "exec" resource that runs the above curl command. this defined type can then be called multiple times within the node-definition in the site.pp, or via ENC/hiera.