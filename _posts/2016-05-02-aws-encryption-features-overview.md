---
ID: 575
post_title: 'AWS &#8211; Encryption features Overview'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-encryption-features-overview
published: true
post_date: 2016-05-02 00:00:00
---
You can encrypt the content of your resources. This basically means that the content can't be viewable by an AWS employee. The only way to decrypt the content is via logging into the AWS Account that created the encrypted data in the first place, and also you need to login with the appropriate account privileges.

There are 3 main resource types, whose data you can encrypt:
<ul>
 	<li>S3 Buckets
<ul>
 	<li>Uses AES-256 encription to <a href="https://en.wikipedia.org/wiki/Data_at_rest" rel="nofollow">encrypt data at rest</a>. It is decrypted only when it receives a valid request, by a valid IAM user or ec2 instance.</li>
</ul>
</li>
 	<li>EBS volumes -
<ul>
 	<li>When enabled, this essentially means that the EC2 instance will first always encrypt the data before sending it to the EBS volume for storage.</li>
 	<li>EBS snapshot therefore only stores encrypted data. only A user with the right IAM role can access this data by mounting it onto a EBS volume.</li>
</ul>
</li>
 	<li>RDS level encryption
<ul>
 	<li>here the underlying EC2 instances encrypts the data before storing it on it's block devices (which could also be an EBS device)</li>
 	<li>Automated backups and snapshots are consequently encrypted too. Note, snapshots are backups that are manually created</li>
 	<li>Read replicas are encrypted too</li>
 	<li>By default RDS endpoints are https rather than http, so that data traffic are also encrypted.</li>
</ul>
</li>
</ul>
&nbsp;

&nbsp;