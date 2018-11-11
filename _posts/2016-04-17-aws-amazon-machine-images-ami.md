---
ID: 563
post_title: 'AWS &#8211; Amazon Machine Images (AMI)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-amazon-machine-images-ami
published: true
post_date: 2016-04-17 00:00:00
---
Note:

AMIs created in one region cannot be used to create instances in another region. However you can get round this by copying the AMI to another region first.

&nbsp;

You can also modify access permissions of the AMI, in the following ways:
<ul>
 	<li>Make the AMI public, so anyone can use it by finding it on the marketplace</li>
 	<li>Specify a list of AWS account names that can access the ami</li>
</ul>
However the region restriction still applies in both of the above cases.

&nbsp;

An AMI is made up of 3 parts:
<ul>
 	<li>a snapshot of the root volume, /dev/sda1</li>
 	<li>launch permissions, i.e. is it a public ami, or restricted to a particular aws account</li>
 	<li>block device mappings. This means that an ami can be made up of several ebs snapshots in addition to /dev/sda1</li>
</ul>
&nbsp;

You can create an AMI from an existing EBS snapshot, as long as the snapshot is taken of the root volume, /dev/sda1.

&nbsp;

&nbsp;

AWS stores Both AMI and snapshots in S3.