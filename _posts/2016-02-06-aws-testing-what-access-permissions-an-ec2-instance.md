---
ID: 535
post_title: 'AWS &#8211; Testing what access permissions an EC2 instance'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-testing-what-access-permissions-an-ec2-instance
published: true
post_date: 2016-02-06 00:00:00
---
In the previous article we cover how to create and log into your instance. During this process, lets say we assigned a role called "test-role" to this instance. 

Next we want to see what other resources our EC2 can access. To do this we first log into our ssh instance from our laptop:

<pre>
[ec2-user@LinuxLaptop ~] $ ssh {ipnumber}
</pre>

Next we need to install the aws cli utilities into our instance:

<pre>
$ yum install python-pip
$ pip install awscli
</pre>


After that we could run the following to see if your machine has access to any s3 buckets:

<pre>
$ aws ls s3
</pre>

This command will list all the buckets that exists under your aws account, assuming that the role that has been assigned to this instance has a mininmum of s3 read only access. The cool thing here is that your EC2 instance was configured with all your AWS account details during build time. That meant that the above command works without the need to provide any other aws account's id, or login credentials.

However this is not the case, if you try to run the above command on your local laptop, which has no info about your aws account. So to interact with your aws account, you need to do it using api keys, which is covered next.   




The cool thing about your exe