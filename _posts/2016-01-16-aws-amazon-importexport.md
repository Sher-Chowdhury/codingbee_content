---
ID: 512
post_title: 'AWS &#8211; Amazon Import/Export'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-amazon-importexport
published: true
post_date: 2016-01-16 00:00:00
---
If you have 20 billion terrabytes of data stored locally and you want to upload them AWS, then that will take a long time and also hog all your network bandwith as well.

That's why in this situation it is better to copy all your data onto hdds and post the hdds to Amazon using courier companies like, Fedex, UPS, DHL...etc. There's 2 ways to to do this:
<ul>
 	<li>Import/Export Disk - you buy a hdd from shop, load it with data, post it to AWS. AWS then loads data into S3/Glacier/EBS then post back your hdd.</li>
 	<li>Import/Export Snowball - Aws posts you with a big heavy duty storage device, which you then load up and post back. Ideal for petabytes of data.</li>
</ul>
Amazon will then import the data into one of the following:
<ul>
 	<li>S3 (disk and snowball)</li>
 	<li>EBS   (disk only)</li>
 	<li>Glacier storage (disk only)</li>
</ul>
Amazon will do this withing 24 hours of receiving the delivery.

&nbsp;

You can also do the reverse, i.e. get Amazon to export your data out of AWS and post it back to you, but this only limited to exporting from S3. This avoids hogging the network bandwith.

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;