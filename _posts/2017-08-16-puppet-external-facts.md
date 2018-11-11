---
ID: 445
post_title: 'Puppet &#8211; External Facts'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-external-facts
published: true
post_date: 2017-08-16 00:00:00
---
External facts is a great way to attach (arbitrary) metadata to a machine during the the launch of a new machine. E.g. when building a Centos 7 aws ec2 instance, you can generate the external facts via <a href="http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html">userdata</a>.  

Puppet can use these external facts in the way as any other fact. You can create external facts by simply creating a file. This file can be a:

<ul>
	<li>shell script</li>
	<li>yaml file</li>
</ul>


This file needs to be created inside any of the following 2 folders on a CentOS machine:

<ul>
	<li>/opt/puppetlabs/facter/facts.d/</li>
	<li>/etc/facter/facts.d</li>
</ul>
Note: you might need to create the above folders if they don't already exist. 



For example, let's say I want to create 3 external facts, the 'key-name=key-values' are:

<pre>
role='blog-site'
pipeline_color='Blue'
standby_mode=true
</pre>

First off, let's confirm that these facts don't already exist:

<pre>
[root@puppetagent1 ~]# facter -p role

[root@puppetagent1 ~]# facter -p pipeline_color

[root@puppetagent1 ~]# facter -p standby_mode

</pre>


First you need to write this information in a shell-script format like this:

<pre>
$ cat /etc/facter/facts.d/my_external_facts.sh
#!/usr/bin/env bash
echo "role=blog-site"
echo "pipeline_color=Blue"
echo "standby_mode=true"
</pre>

Then make the script executable: 

<pre>
[root@puppetagent1 ~]# chmod ugo+x /etc/facter/facts.d/my_external_facts.sh
</pre>

After that you should find the new external facts are now working:

<pre>
[root@puppetagent1 ~]# facter -p role
blog-site
[root@puppetagent1 ~]# facter -p pipeline_color
Blue
[root@puppetagent1 ~]# facter -p standby_mode
true
</pre>

Note, instead of creating a shell script, we could have created the following yaml file:


<pre>
[root@puppetagent1 ~]# cat /etc/facter/facts.d/my_external_facts.yaml
---
role: blog-site
pipeline_color: Blue
standby_mode: true
</pre>

This has the exact same end result:

<pre>
[root@puppetagent1 ~]# facter -p role
blog-site
[root@puppetagent1 ~]# facter -p pipeline_color
Blue
[root@puppetagent1 ~]# facter -p standby_mode
true
</pre>
 

In this guide I have covered a few simple examples. The truth is that the external facts file isn't just limited to either a shell script or yaml file. You can write it in lots of other forms, e.g. python script, json file,...etc. For more info, see the official <a href="https://docs.puppet.com/facter/latest/custom_facts.html#what-are-external-facts">external facts</a> documentation. 


If you are creating your external facts file via ec2 userdata, then here's a handy example snippet to place inside your userdata script:

<pre>
#!/usr/bin/env bash

role='blog-site'
pipeline_color='Blue'
standby_mode='true'
 
cat >/opt/puppetlabs/facter/facts.d/my_external_facts.sh << EOF
#!/usr/bin/env bash
echo "role=${role}"
echo "pipeline_color=${pipeline_color}"
echo "standby_mode=${standby_mode}"
EOF

chmod 0755 /opt/puppetlabs/facter/facts.d/my_external_facts.sh

</pre>