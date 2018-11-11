---
ID: 572
post_title: 'AWS &#8211; Shared (Security) Responsibility Model'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-shared-security-responsibility-model
published: true
post_date: 2016-05-02 00:00:00
---
Ensuring that your aws infrastructure is secure is a responsibility that's shared between you and Amazon.

Amazon is responsible for mainly:
<ul>
 	<li>Ensuring physical hardware that your resources (e.g. EC2 instances are running on). E.g. limit access to who is allowed to walk into AWS's AZs (data centres)</li>
 	<li>Ensuring that internal data transfers are secure, e.g. data transfers between S3 buckets and EC2 instances. Also data transfers between physical hardware</li>
</ul>
We are responsible for:
<ul>
 	<li>Ensuring we use AMIs that are secure, i.e. don't have api keys or ssh keys hardcoded in them.</li>
 	<li>Performing OS software updates and security patches</li>
 	<li>Keeping "<a href="https://en.wikipedia.org/wiki/Data_at_rest" rel="nofollow">Data at rest</a>" secure - e.g. persistant data on our EBS. We can select the ebs encrypt option when creating our instances, also encrypt our filesystems using luksformat.</li>
 	<li>OS configurations, e.g. firewalld and selinux</li>
 	<li>software configurations, e.g. httpd settings</li>
 	<li>Setting up ssl certificates</li>
 	<li>Install firewalls</li>
 	<li>securely accessing AWS, via bastion host, vpn, or AWS Direct Connect</li>
 	<li>Properly configuring security groups and network acls</li>
 	<li>ensuring our own developed apps are secure, e.g. add a login page to our apps, prompting user to log in, in order to access data.</li>
</ul>
&nbsp;

&nbsp;

https://aws.amazon.com/compliance/shared-responsibility-model/