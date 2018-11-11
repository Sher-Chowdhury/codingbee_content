---
ID: 511
post_title: 'AWS &#8211; Amazon Storage Gateway'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-amazon-storage-gateway
published: true
post_date: 2016-01-16 00:00:00
---
Amazon Storage Gateway is a service that's commonly used in conjunction with Amazon S3.

A Storage gateway is a virtual device that you install in your onsite premise, this device then sends data to be stored in AWS, usually S3 or Glacier.

It is service the monitors a local copy of your objects and keeps a copy of them in S3. If your local copy changes, then the copy that is held in S3 changes as well in order to stay in sync. This lets you have a realtime back up of your local data held in S3.

This virtual device is essentially a vm image. To set this up, you do:
<ul>
 	<li>start up a vm from this image.</li>
 	<li>configure the vm to connect to your aws account</li>
 	<li>perform the activation process, e.g. handshake.</li>
</ul>
In the aws console, you then need to choose what type of storage gateway you want to use.

&nbsp;

You can configure "Amazon Storage Gateway" to work in 1 of 2 ways:
<ul>
 	<li><b>Gateway-Cached Volumes:</b>  Here you store your  data in Amazon S3, and retain your frequently accessed data locally. This is a cheaper option because it cuts down data traffic to/from S3 and reduces the amount of local storage you need. However the downside is that there is higher latency if you try to access an object that isn't locally cached.</li>
 	<li><b>Gateway-Stored Volumes:</b>  Here all data are stored locally, however Amazon Storage Gateway will take an initial snapshot followed by regular periodic incremental snapshots and store these snapshots to S3.</li>
 	<li><strong>Gateway Virtual tape Library (VTL)</strong>:   Used for doing backups via other third party apps, e.g. Netbackup</li>
</ul>
To summarize,  <b>Gateway-Cached Volumes  </b>essentially works like a cloud based storage solution whereas  <b>Gateway-Stored Volumes</b> whereas works more like a backup/Distaster-recovery solution.

&nbsp;

&nbsp;

&nbsp;

https://aws.amazon.com/storagegateway/

&nbsp;

This I think is how dropbox works.