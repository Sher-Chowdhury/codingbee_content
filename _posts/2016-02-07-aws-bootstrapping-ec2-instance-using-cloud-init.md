---
ID: 539
post_title: 'AWS &#8211; Bootstrapping EC2 instance using cloud-init'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-bootstrapping-ec2-instance-using-cloud-init
published: true
post_date: 2016-02-07 00:00:00
---
When you create a new EC2 instance, there is a chance you want to run a series of shell scripts to further prepare the instance before it is ready for use. This is possible using a tool called <a href="https://cloudinit.readthedocs.org/en/latest/" rel="nofollow">cloud-init</a>.

You have to pass in "user-data" into cloud-init.

As a sidenote, After your instance is built, you can view this data, by going to the following url from inside your instance:
<pre>http://169.254.169.254/latest/meta-data</pre>
You can use the same method, to view your instance general meta data:
<pre>http://169.254.169.254/latest/user-data</pre>
Note: you have to be inside the instance before querying the above url.

&nbsp;
<pre>
$ curl http://169.254.169.254/latest/       # notice the last trailing slash, which makes curl act a bit like the ls command. 
dynamic
meta-data
user-data
</pre>
&nbsp;

These 2 urls are useful, if you want to run a ruby/python/bash script from inside your instance and want to use some of this data.

for example here is a sample output if you use curl while logged inside of your instance:


<pre>
$ curl http://169.254.169.254/latest/meta-data
ami-id
ami-launch-index
hostname
.
.
instance-id
public-ipv4
.
.
...etc
</pre>

This show list of "keys" part of key-value pairs. To get the value, for a key, e.g. for public-ipv4, you do:

<pre>
$ curl http://169.254.169.254/latest/meta-data/public-ipv4
152.12.131.152
</pre>


&nbsp;

The user-data on the other hand displays your custom script that you might have added, e.g.:

<pre>
$ curl http://169.254.169.254/latest/user-data
#!/bin/bash
echo "hello world"
export env_variable1=value    
yum install httpd -y
</pre>
 
Note, I think the user-data is only available during the instance creation time. If you reboot the vm afterwards, this info gets lost...I think.  

Cloud-init has a setting that disables people from logging in as root, this setting is specified in:

<pre>
/etc/cloud/cloud.cfg
</pre>

This has an ini setting that is:

<pre>
disable_root: root
</pre>

This actually overrides what sshd's "permitrootlogin" setting is set to. However this cloud setting is only effective once a vm is created and running for the first time. As soon as you reboot, this setting is no longer enforced.  






http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-metadata.html

&nbsp;

http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html