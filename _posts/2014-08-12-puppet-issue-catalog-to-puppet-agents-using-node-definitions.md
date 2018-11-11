---
ID: 136
post_title: 'Puppet &#8211; Issue catalog to puppet agents (using node definitions)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-issue-catalog-to-puppet-agents-using-node-definitions
published: true
post_date: 2014-08-12 00:00:00
---
So far we have looked at how to:
<ol>
	<li>how to write a resource in the form of a resource declaration</li>
	<li>store the resource in a manifest file (aka a puppet program)</li>
	<li>Apply the resource to the local machine.</li>
</ol>
&nbsp;

We did all of this on the puppet master and we so far ignored that we also had 2 puppet agents to play with.

However the primary reason for the Puppet master/agent architecture is so that all the puppet agents are managed centrally via the puppet master. To do this we need to use the site.pp file:

<pre>
/etc/puppet/manifests/site.pp
</pre>

Note: this file only resides in the puppet master. 

The puppet master always reads one special manifest, called the “site manifest” or site.pp. It always reads this to compile a catalog, which it sends back to the agent. 

Once the agent receives the catalog, it then applies the resources that are defined in the catalogue.

This means that you can define resources in the site.pp and it will in turn get sent to the puppet agents. We can apply resources to a particalur agent by using the <a href="https://docs.puppetlabs.com/puppet/latest/reference/lang_node_definitions.html">node definitions</a> syntax. 

The first time you open this file, you will find this to be empty. Lets say you enter the following 3 resources:

<pre>
[root@puppetmaster manifests]# cat site.pp
# This will be applied to all agents
file {'AgentTestfileALL.txt':
    ensure  => present,
    path    => '/tmp/AgentTestfileALL.txt',
    mode    => 0777,
    content => "Hello ALL agents \n",
}

node 'PuppetAgent1' {

        file {'AgentTestfile1.txt':
            ensure  => present,
                path    => '/tmp/AgentTestfile1.txt',
                mode    => 0777,
                content => "Hello agent 1 \n",
        }
}

node 'PuppetAgent2' {

        file {'AgentTestfile2.txt':
            ensure  => present,
                path    => '/tmp/AgentTestfile2.txt',
                mode    => 0777,
                content => "Hello agent 2 \n",
        }
}
</pre>


This manifest indicates that the first resource should get applied to all agent's whereas the 2nd and 3rd are encased within a node definition. 


&nbsp;
Now for each of my agent's I'll manually trigger an update, for puppetagent1, we have:

<pre>
[root@puppetagent1 ssl]# puppet agent --test
Info: Caching certificate for puppetagent1.codingbee.dyndns.org
Info: Caching certificate_revocation_list for ca
Info: Caching certificate for puppetagent1.codingbee.dyndns.org
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Caching catalog for puppetagent1.codingbee.dyndns.org
Info: Applying configuration version '1407806502'
Notice: /Stage[main]/Main/Node[puppetagent1]/File[AgentTestfile1.txt]/ensure: created
Notice: /Stage[main]/Main/File[AgentTestfileALL.txt]/ensure: created
Notice: Finished catalog run in 0.08 seconds
[root@puppetagent1 ssl]#
</pre>

And for Puppetagent2, we have:

<pre>
[root@puppetagent2 sysconfig]# puppet agent --test
Info: Caching certificate for puppetagent2.codingbee.dyndns.org
Info: Caching certificate_revocation_list for ca
Info: Caching certificate for puppetagent2.codingbee.dyndns.org
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Caching catalog for puppetagent2.codingbee.dyndns.org
Info: Applying configuration version '1407806502'
Notice: /Stage[main]/Main/Node[puppetagent2]/File[AgentTestfile2.txt]/ensure: created
Notice: /Stage[main]/Main/File[AgentTestfileALL.txt]/ensure: created
Info: Creating state file /var/lib/puppet/state/state.yaml
Notice: Finished catalog run in 0.10 seconds
[root@puppetagent2 sysconfig]# 
</pre>


As you can see, this approach is going to result in a really long site.pp file. The first probelm 
&nbsp;