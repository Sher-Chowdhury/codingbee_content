---
ID: 538
post_title: 'AWS &#8211; Overview of Elastic Block Storage (EBS)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-overview-of-elastic-block-storage-ebs
published: true
post_date: 2016-02-07 00:00:00
---
EBS is the way to add additional block device storage to your ec2 instance. Once attached it is still up to you to do all the tasks you need to mount it, e.g. use fdisk, mkfs.ext4, add entry to /etc/fstab...etc.

Note, you can only attach EBS devices to the an EC2 that is in the same AZ. But you can overcome this using snapshots, covered further down.

There are different types of EBS storage, each offering different levels of max capacity and performance. In terms of performance, the key performance indicator is "IOPS".

&nbsp;

<strong>IOPS</strong>: Input/output Operations Per Seconds, where the data processed in each operation is 256kb in size.

IOPS is just an alternative measurement for network bandwidth.  Therefore if I an EC2 instance can interact with an EBS at 20000 IOPS, then in real term bandwith terms, this works out to be:

256kb * 20000 = 5120000kb

5120000 / 1024 = 5000 MB/s = 5GB/s.

If money was no issue, then to get the best performance you need to:
<ul>
	<li>use a EBS-optimised EC2 instance</li>
	<li>use a Network optimised EC2 instance (to ensure good bandwidth)</li>
	<li>An EBS type with high IOPS rating.</li>
</ul>
&nbsp;
<h2>Types of EBS volumes</h2>
There are three types of EBS volumes:
<ul>
	<li>EBS General Purpose (SSD)</li>
	<li>EBS Provisioned IOPS (SSD)</li>
	<li>EBS Magnetic</li>
</ul>
&nbsp;

&nbsp;
<h3>EBS General Purpose (SSD)</h3>
This is
<ul>
	<li>Used for EC2 instance's where an EBS volume is the primary block device, i.e. <a href="https://aws.amazon.com/ec2/instance-types/">T2, M4, and C4 instances</a>.</li>
	<li>used for small dev/test environments and hosting small dbs</li>
	<li>By default you get 3IOPS per GIB - which is burstable, this means you can accrue unused IOPS and use when there is a sudden surge. You can burst up to 3000 IOPS</li>
	<li>Size can range from 1GiB to 16TiB</li>
	<li>ideal for vm's that usually experiences low load, and the occassional highload.</li>
</ul>
<h3>Provisioned IOPS</h3>
This offers better performance then the General Purpose EBS:
<ul>
	<li>Suitable for productions  apps that requires continuously high IOPS in order to prevent bottlenecks</li>
	<li>suitable for hosting large databases</li>
	<li>Size can range from 4GiB to 16 TiB</li>
	<li>Max Baseline IOPS is 20000 IOPS</li>
</ul>
&nbsp;
<h3>EBS Magnetic</h3>
<ul>
	<li>This is the cheapest option</li>
	<li>ideal when high performance is not important.</li>
	<li>has low IOPS</li>
	<li>Size can range from 1GiB-1024GiB (1TiB)</li>
</ul>
&nbsp;

&nbsp;
<h2>Reusing an EBS volume</h2>
If you detach an EBS volume from one EC2 instance and attach it to another, then all the data gets delete automatically for security reasons. this Auto deletions ends up using   up a lot of IOPS. That's why it's better to delete the EBS's content using the "dd" command before detaching it.

&nbsp;

&nbsp;
<h2>EBS Snapshots</h2>
You can enable snapshots for your EBS volumes. These are incremental snapshots.

Snasphots increases data durability. However performance does get impacted while a snapshot is occuring, that's why it's best to schedule snapshots to take place during off peak periods.

You can delete any snapshots as well.

snapshots are stored in S3 behind the scenes. However you can view these snapshots in S3 since they are all hidden away. Instead you view your available snapshots from inside EC2 section instead.

when creating an EBS, you have the option to specify a snapshot ID. This means that you can create another EBS in different AZ and use a snapshot generated from an EBS that was in a  another  AZ. This is a possible way to replicate an EBS to another EBS in a different AZ.   It is also a good way to switch between different EBS types, e.g. move from General Purpose EBS to Provisioned IOPS EBS.

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-io-characteristics.html

&nbsp;

&nbsp;

&nbsp;

https://aws.amazon.com/ebs/details/