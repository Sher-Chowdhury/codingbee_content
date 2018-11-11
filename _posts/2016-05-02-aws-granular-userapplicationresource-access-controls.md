---
ID: 576
post_title: 'AWS &#8211; Granular User/Application/Resource Access Controls'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-granular-userapplicationresource-access-controls
published: true
post_date: 2016-05-02 00:00:00
---
If there is a particular file in an S3 bucket that is available to access, then there are three things that may want to download it:
<ul>
 	<li>An AWS user - This request can be granted via IAM roles</li>
 	<li>An AD user, who doesn't have an AWS account - this user might want temporary access, in which this can be done via a token that allows them to temporarily assume a role.</li>
 	<li>An Application that's running on top of a resource (e.g. ec2 instance) - this request can be granted via tokens that have expiry dates</li>
 	<li>Resources - e.g. an ec2 instance itself might want to download the file. In which case this can be done by assigning the necessary permissions to the EC2 instance's IAM role.</li>
</ul>
&nbsp;

You can set up IAM roles that are specify permissions on specific resources, .e.g. EC2 resources.