---
ID: 299
post_title: 'Puppet &#8211; External Node Classifiers (ENC)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-external-node-classifiers-enc
published: true
post_date: 2015-02-22 00:00:00
---
If you have stored all your puppet resources/logic/classes into modules, and you have placed all your configuration data (i.e. class parameter data) into hiera, then your site.pp file should now be just contain a series of node defintion, where each node definition is just a series include statements. 


When ever a puppet agent requests for a new catalog, the puppetmaster will first ask the agent to provide all facter+builtin data. It then looks for the agent's node definition in the site.pp file. However you can configure the puppetmaster to look for the node definition from an alternative source, this source is known as an ENC. In this situation, the puppetmaster is designed to retrieve the node definition in the form of yaml code, rather than puppet code as in the site.pp file. This means that when the puppetmaster queries the ENC, and the ENC must give any output in the form of yaml code. In reality, the ENC can be any kind executable, e.g. python/ruby/shell/java script/executables. The script doesn't need to have complex code to generate the node definition in yaml form, instead it can simply locate a existing yaml file that we manually created earlier, whic already holds the appropriate node definition and then output it back to the puppetmaster. We'll now show you how to create your very own enc now.     



We have already abstracted away most of the code already by fully utilizing the power of puppet modules and hiera. This has left the site.pp file to only contain a series of node definitions, and each node definition contains a series of include statements. 

The key thing to realise at this point is that we can now represent each of our node definitions in the form of yaml syntax. Hence we can create a yaml file equivalent of each node definition. For example if our site.pp contains just 2 node definitions:

<pre>
[root@puppetmaster manifests]# cat /etc/puppet/manifests/site.pp
node 'puppetmaster' {
  include user_account
}

node 'puppetagent' {
  include user_account
}
</pre>


Then we can create yaml file for each of them, and let's name each yaml file after the agent's fqdn. In my case I have placed these files in /tmp/node-definitions, i.e.:


<pre>
[root@puppetmaster ~]# cd /tmp/node-definitions/
[root@puppetmaster node-definitions]# ls -l
total 8
-rw-r--r--. 1 root root 32 Feb 22 12:53 puppetagent1.codingbee.dyndns.org.yaml
-rw-r--r--. 1 root root 32 Feb 22 13:49 puppetmaster.codingbee.dyndns.org.yaml
[root@puppetmaster node-definitions]# cat puppetmaster.codingbee.dyndns.org.yaml
---

classes:
  - user_account

[root@puppetmaster node-definitions]# cat puppetagent1.codingbee.dyndns.org.yaml
---

classes:
  - user_account

[root@puppetmaster node-definitions]#

</pre>

Now we can delete all the contents of site.pp, so that it becomes an empty file:

<pre>
[root@puppetmaster /]# cd /etc/puppet/manifests/
[root@puppetmaster manifests]# echo "" > site.pp
[root@puppetmaster manifests]# cat site.pp

[root@puppetmaster manifests]#
</pre>

Next we need to create a script that the puppetmaster can query to retrieve the node definition in yaml form. In my case I have created a script called /etc/puppet/enc.sh. This script is just made up of 3 lines:

<pre>
[root@puppetmaster ~]# cat /etc/puppet/enc.sh
#!/bin/bash

node_def="/tmp/node-definitions/${1}.yaml"

[ -f $node_def ] && cat $node_def
[root@puppetmaster ~]#

</pre>

As you can see, this script only makes use of one script parameter, ${1}, which is used to derived a possible path to a yaml file that could contain the node-definition in yaml form. The script then checks if the yaml file exists, if so then it output's it's content.  

Now that we have the external node classifier (enc.sh) in place along, we now need to <a href="https://docs.puppetlabs.com/guides/external_nodes.html#connecting-an-enc">tell the puppetmaster to start using the enc</a>. We do this by configuring 2 settings in the puppet.conf file, the <a href="https://docs.puppetlabs.com/references/latest/configuration.html#nodeterminus">node_terminus</a> setting and the <a href="https://docs.puppetlabs.com/guides/external_nodes.html">external_nodes</a> setting. These are puppetmaster setting and are defined under the master section. In our case we add the following to our puppet.conf file:

<pre>
[master]
  node_terminus = exec
  external_nodes = /etc/puppet/enc.sh
</pre>
  
Now we restart the puppetmaster to load the new puppet.conf settings into memory:

<pre>
[root@puppetmaster puppet]# service puppetmaster restart
Stopping puppetmaster:                                     [  OK  ]
Starting puppetmaster:                                     [  OK  ]
[root@puppetmaster puppet]#
</pre>

Now here's a summary of our puppetmaster setup for the agent, puppetagent1:

<pre>
##
## Here we determine the location of the node defintition in yaml form:
##

[root@puppetmaster puppet]# cat /etc/puppet/enc.sh
#!/bin/bash

node_def="/tmp/node-definitions/${1}.yaml"

[ -f $node_def ] && cat $node_def


##
## Here we determine the classes that are included in the node definition:
##

[root@puppetmaster puppet]# cat /tmp/node-definitions/puppetagent1.codingbee.dyndns.org.yaml
---

classes:
  - user_account

##
## Here we determine what class parameters the class needs:
##

[root@puppetmaster puppet]# cat /etc/puppet/modules/user_account/manifests/init.pp
class user_account ($username = "tempuser"){

  user { "$username":
    ensure => present,
    shell  => '/bin/bash',
  }


  file {"/tmp/$username.txt":
    ensure => file,
    content => $var,
  }

}



##
## Here we determine where the yaml datasource files are kept:
##

[root@puppetmaster puppet]# cat /etc/hiera.yaml
 ---
:backends:
  - yaml
:yaml:
  :datadir: /etc/puppet/hieradata/yaml
:hierarchy:
  - %{hostname}
  - filaA
  - fileB
  - common


##
## Here we determine what class parameter value will be used:
##
[root@puppetmaster puppet]# cat /etc/puppet/hieradata/yaml/puppetagent1.yaml
---

user_account::username: Tom
[root@puppetmaster puppet]#

</pre>

Let's now go ahead and do the puppet run:

<pre>
[root@puppetagent1 ~]# cat /etc/passwd | grep "Tom"
[root@puppetagent1 ~]# ls -l /tmp/Tom.txt
ls: cannot access Tom.txt: No such file or directory
[root@puppetagent1 ~]# puppet agent -t
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Caching catalog for puppetagent1.codingbee.dyndns.org
Info: Applying configuration version '1424625777'
Notice: /Stage[main]/User_account/User[Tom]/ensure: created
Notice: /Stage[main]/User_account/File[/tmp/Tom.txt]/ensure: created
Notice: Finished catalog run in 0.10 seconds
[root@puppetagent1 ~]# cat /etc/passwd | grep "Tom"
Tom:x:505:506::/home/Tom:/bin/bash
[root@puppetagent1 ~]# ls -l /tmp/Tom.txt
-rw-r--r--. 1 root root 0 Feb 22 17:22 /tmp/Tom.txt
[root@puppetagent1 ~]#

</pre>

Success!!!

</a>See also:

http://www.cyberciti.biz/faq/unix-linux-test-existence-of-file-in-bash/

https://docs.puppetlabs.com/guides/external_nodes.html

https://docs.puppetlabs.com/references/latest/configuration.html#nodeterminus