---
ID: 138
post_title: 'Puppet &#8211; Ordering your resources'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-ordering-your-resources
published: true
post_date: 2014-08-13 00:00:00
---
In Puppet, the resources in a manifest can potentially be applied in any order, rather than in the order it has been specified in the manifest. One way round this is to insert the following:

ordering = manifest 

in the puppet.conf file. This is a good idea to do. On top of that you can override this with the use of special attributes called <strong>metaparameters </strong>which you can embed within your resource declarations.  


&nbsp;

The 4 main metaparameters for controlling the order that resources are:

<ul>
	<li><code>before</code></li>
	<li><code>require</code></li>
	<li><code>notify</code></li>
	<li><code>subscribe</code></li>
</ul>

Note: there is a also a resource type called "notify". But here we are referring to the notify metaparameter which is a different thing. 

These 4 are similar and can be paired up like this:
<ul>
	<li>before and require</li>
	<li>notifiy and subscribe</li>
</ul>
&nbsp;
<h2>"Before" and "Require"</h2>
Here is the "before" attribute in action:

<pre> 
file {'/tmp/test1':
      ensure  =&gt; present,
      content =&gt; "Hi.",
 }

 notify {'/tmp/test1 has already been synced.':
      <strong style="color:red">require =&gt; File['/tmp/test1'], </strong>                  # Note, you must capitalise, i.e. "File" and not "file"
 }
</pre>
The above says that the "notify" resource requires the "/tmp/test1" resource to be applied first before it itself can be applied.

The above can also be rewritten using the "before" metaparameter to:

&nbsp;
<pre> file {'/tmp/test1':
      ensure  =&gt; present,
      content =&gt; "Hi.",
      <strong style="color:red">before  =&gt; Notify['/tmp/test1 has already been synced.'],</strong> # Again "Notify" and not "notify"

 } 

 notify {'/tmp/test1 has already been synced.':}

</pre>
&nbsp;

Here we are saying that the '/tmp/test1' file resource has to be applied before the notify resource.

As you can see we have done some capitalisation. The rule is that you <em>only</em> type in lowercase (when writing the resrouce's type) when <em>declaring a new resource.</em> Any other situation will always call for writing in uppercase.

&nbsp;

&nbsp;
<h2>"Subscribe" and "Notify"</h2>
These are similar to require/before, in terms of ordering your resources. However it also has an extra "notification" feature which is used to "refresh" a resource.

This is useful if you make changes to a config file and then want to restart (aka refresh) the corresponding service so that it loads the new settings into memory.

In this situation puppet is smart enough to do resource refresh. E.g. if you have declared a service resource's "ensure" attribute set as "running" then if that resource get's applied by subscribe/notify, then it will restart itself. Here's an example:

&nbsp;
<pre>
file { '/etc/ssh/sshd_config':
      ensure =&gt; file,
      mode   =&gt; 600,
      source =&gt; 'puppet:///modules/ssh/sshd_config',
    }
service { 'sshd':
      ensure    =&gt; running,
      enable    =&gt; true,
      subscribe =&gt; File['/etc/ssh/sshd_config'],
    }
</pre>
In this example, the sshd service will be restarted if Puppet has to edit its config file, called "sshd_config".

Here we used the "subscribe" attribute. But we can also achieve the same outcome using the notify attribute:

&nbsp;
<pre>file { '/etc/ssh/sshd_config':
      ensure =&gt; file,
      mode   =&gt; 600,
      source =&gt; 'puppet:///modules/ssh/sshd_config',
      <strong style="color:red">notify =&gt; Service['sshd'],</strong>
    }
service { 'sshd':
      ensure    =&gt; running,
      enable    =&gt; true,

    }
</pre>
&nbsp;

&nbsp;
<h2>Chaining Arrows</h2>
Both the before+after and notify+subscribe relationships can be written in short-hand form with the help of the "chaining arrows" syntax. Chaining arrows also makes your code easier to read.

&nbsp;

So far we have looked at 2 ways to represent the same ordering. One using the "Before" parameter and the other using the "after" parameter. However there are 2 more ways, but this time using chaining arrows "-&gt;"

&nbsp;
<pre>
 file {'/tmp/test1':
      ensure  => present,
      content => "Hi.",
 }

 notify {'after':
      message => '/tmp/test1 has already been synced.',
 }
 
 File['/tmp/test1'] -> Notify['after']           # here we used "->" to show the order. 
</pre>
This is a lot easier to to read, and doesn't require you to user the before/after metaparameters. Another even simpler way to write the above is:

<pre> 
file {'/tmp/test1':
      ensure  =&gt; present,
      content =&gt; "Hi.",
}
 <strong style="color:red">-&gt;</strong>
notify {'after':
      message =&gt; '/tmp/test1 has already been synced.',
}
</pre>
&nbsp;

We can do the same thing with subscribe/notify, but using "~&gt;" instead of "-&gt;".


<h2>A Common Scenario</h2>
&nbsp;
You often find yourself performing a the following sequence of tasks:

<ul>
	<li>install package</li>
	<li>update config files</li>
	<li>start service</li>
</ul>


In thi this situation you use the subscribe/notify to help sequence these tasks. 

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;
<h2 id="autorequire">Autorequire</h2>
Sometimes puppet is smart enough to realise an order even if it hasn't been explicitly specified. for example you have two resources, one of them creates a folder, and the other creates a file within that folder. In this situation puppet is smart enough to apply the folder resource followed by the file resource.

However it is best practice to not rely on autorequire and just specify the order explicitly.

&nbsp;

&nbsp;
<h2></h2>
&nbsp;

https://docs.puppetlabs.com/learning/ordering.html

&nbsp;

https://docs.puppetlabs.com/puppet/latest/reference/lang_relationships.html