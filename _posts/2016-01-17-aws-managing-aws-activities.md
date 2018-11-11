---
ID: 523
post_title: 'AWS &#8211; Managing AWS activities'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-managing-aws-activities
published: true
post_date: 2016-01-17 00:00:00
---
<h2>Identity Access Management (IAM)</h2>
IAM is a service that lets you set user and group permissions on what they are allowed/denied to do.

&nbsp;

It lets you set permissions for resources that belong to the following service categories:
<ul>
	<li>computing</li>
	<li>storage</li>
	<li>databases</li>
	<li>applications</li>
</ul>
These permissions are set specified against particular API-calls/CLI-options/web-gui-console

Here are some examples:
<ul>
	<li>give a user (or group) permission to create new EC2 instances</li>
	<li>deny user (or group) permssions to delete an particular EC2 instance</li>
</ul>
&nbsp;

IAM is very granular and let's you set all kinds of permissions.

http://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html

&nbsp;
<h2>CloudTrail</h2>
This is a service that logs all aws-console/cli/api activities and who performed them.

It is a logging solution to help identify any security issues.

https://aws.amazon.com/cloudtrail/

&nbsp;
<h2>Cloudwatch</h2>
This is a monitoring service that monitors various service and resources. It can collect and track metrics. It can collect logs for various resources, e.g. cpu utilisation on a given EC2 instance, network bandwith usage....etc.

Cloudwatch ties in with auto-scaling quite closely. E.g. you can instruct cloudwatch to scale-up if cpu usage exceeds 80% or if queue size exceeds 5000 jobs.

&nbsp;

https://aws.amazon.com/cloudwatch/

&nbsp;
<h2>Directory services</h2>
This service allows you to create and sync AWS users and groups based on local Microsoft Active-Directory Server. Alternatively you can create a new  Microsoft Active Directory (AD) service inside AWS and sync it up with a local Microsoft AD server.

This makes single sign on possible.

https://aws.amazon.com/directoryservice/