---
ID: 504
post_title: 'AWS &#8211; Amazon Elastic Cloud Compute (EC2) overview'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-amazon-elastic-cloud-compute-ec2-overview
published: true
post_date: 2016-01-13 00:00:00
---
<a href="https://aws.amazon.com/ec2/" rel="nofollow">EC2</a> is arguably the most important service available on the AWS platform,  EC2 is used for creating VMs on the AWS platform. These vms are referred to as EC2 "<strong>instances</strong>". There are <a href="https://aws.amazon.com/ec2/pricing/" rel="nofollow">three pricing models</a> available under EC2:

&nbsp;
<h2>Pricing</h2>
<ul>
	<li><strong>On-Demand Instances</strong>: This lets you pay for computing capacity by the hour with no long-term commitments.</li>
	<li><strong>Reserved Instances</strong>: This reserves an instance's resource exclusively for your use. You get charged even if you don't use them. But it garauntees that this resource is available in the given AZ. You can book your instance a period of one month, to up to 3 years. You can also sell it if you don't need it anymore. You also have an option to ensure this vm runs on the same physical machine throughout the term.</li>
	<li><strong>Spot Instances</strong> - Spot Instances enable you to bid for unused Amazon EC2 capacity. Only recommended to use this for doing things like processing non-production stuff.</li>
</ul>
You don't pay for the EC2 while it is switched off.  However you still have to pay for  any EBS devices attached to your EC2 instance.
Later on you will find the <a href="http://calculator.s3.amazonaws.com/index.html">aws cost calculator</a> really handy.

&nbsp;
<h2>EC2 features</h2>
<ul>
	<li>Autoscaling/elastic</li>
	<li>highly availablle</li>
	<li>fault tolerant</li>
	<li>Tightly integrates with Elastic Load Balancer, so that an ELB can evenly distirbute work to a cluster of servers, that can reside across multiple AZs, within the same region.</li>
	<li>Tightly integrates with Elastic Block Storage (EBS) - which is how we added network based block devices to the vm.</li>
</ul>
&nbsp;
<h2>Instance Types</h2>
Since EC2 instance have built in auto-scaling it means that we don't need to build an 10 CPU vm, where 4 cpus are required for nearly the whole year and only 10 CPU is require during the Christmas period. Instead, we only request for what we need.

Instead the specs we need should be based on the applicaton's needs. AWS provides the following predefined profiles to choose from. These profiles are referred to as <a href="https://aws.amazon.com/ec2/instance-types/">instance types</a>, here are the main ones:
<ul>
	<li>T2 - These are comparitively quite low spec, but they have Burstable Performance Instance, which means that can handle occassional high demands. You have a few T2 specs to choose from:
<ul>
	<li>t2.nano</li>
	<li>t2.micro</li>
	<li>t2.small</li>
	<li>t2.medium</li>
	<li>t2.large</li>
</ul>
</li>
	<li>M4 -  These are more general purpose VMs. These are EBS storage only, and no SSD.  This means that this is also EBS optimised i.e.  bigger bandwidth</li>
	<li>M3 - like M4, but with SSD</li>
	<li>C4 - These vms have emphasis on  cpu power and EBS storage only, and no SSD. This means that this is also EBS optimised i.e. bigger bandwidth</li>
	<li>C3 - Like C4 but with SSD</li>
	<li>R3 - these have more emphasis on RAM</li>
	<li>G2  - these have more emphasis on GPU pcrocessor</li>
	<li>I2  - these have more emphasis on storage. I.e. large ssd instead of EBS for fast I/0</li>
	<li>D2 - Like I2 but with even more storage but in HDD form. This is EBS optimised as well.</li>
</ul>
&nbsp;
<h2>Elastic Network Interfaces</h2>
When you create a new instance, it normally has one default interface, which is eth0, however you can add additional Interfaces, e.g. eth1, et2...etc. There is a limit to how many interfaces you can add, and this limit depends on the instance size. e.g. you can attach more instances on a large instance compared to a nano instance.

&nbsp;
<h2>Elastic IP address</h2>
When you create a new instance, you instance automatically gets to ip addresses:
<ul>
	<li>public ip address - this address is not persistant and will change if you shutdown your instance and then start it up again.</li>
	<li>primary private ip address - this is persistant and used by other resources that are inside your vpc to communicate with each other.</li>
</ul>
If you want to have a persistent public IP address, then you can achieve this by creating an "Elastic IP Address" and link it up to your private IP address.

&nbsp;

&nbsp;
<h2><strong>Instance Storage</strong></h2>
Your EC2 instance's primary block device can be either one of the following types of block device:
<ul>
	<li>Instance Store-backed instances (aka ephemeral storage)</li>
	<li>EBS (Elastic block store) backed storage</li>
</ul>
In AWS, the primary block device is referred to as <strong>/dev/sda1</strong>. In fact /dev/sda doesn't exist in the world of aws.

The block device type that your instance ends up is dictated by choice of AMI.

&nbsp;
<h3><strong>Instance Store-backed instances (aka ephemeral storage)</strong></h3>
This is temporary storage for the life of the instance. Here are the main features:
<ul>
	<li>Data in this  storage gets deleted when the instance is switched-off/shutdown. In fact, the switched-off/shutdown option is actually grayed out, and you can only terminate them.   However the data  does survive a reboot.</li>
	<li>They are virtual storage that is creating on top of actual SSD devices.</li>
	<li>These SSD hardware is actually physically attached to the hardware that is hosting your instance. In order to maximise performance.</li>
	<li>If there are things you want to persist when booting up a power-offed instance, then you should prebake those into the AMI that your instance was built from. Alternatively you can run a bunch of shell scripts after bootup, this kind of "bootstrapping" is possible using a technology called <a href="http://codingbee.net/tutorials/aws/aws-bootstrapping-ec2-using-cloud-init/">cloud-init</a>.</li>
</ul>
To create an instance with an ephemeral root device, you nee to choose an ami which is compatible to this setup:
http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/RootDeviceStorage.html#choose-an-ami-by-root-device

In reality, most of the AMIs in the AMI marketplaces are EBS based. Very few are ephemaral storage based.

&nbsp;
<h3><strong>EBS (Elastic block store) backed storage</strong></h3>
These are network attached storage.
<ul>
	<li>You can take snapshots of this and store it in Amazon S3.</li>
	<li>T2, M4, and C4 uses an EBS for their primary block device. This means you can bootup a powered-off instance without any data loss.</li>
	<li>If your instance needs to do a lot of reading/writing to the EBS, then this can be a bottleneck. However you can overcome this by one of the following ways:
<ul>
	<li>allocating additional IOPS to your instance</li>
	<li>Create an EBS optimised instance to begin with.</li>
	<li>ebs sizes can be anywhere between 1gb to 16TB (16000gb)</li>
	<li>ebs must resided be in the same AZ as the instance that it is connected to.</li>
	<li>ebs can only be attached to one instance at a time.</li>
</ul>
</li>
</ul>
&nbsp;
<h2><strong>EBS-optimized Instances</strong></h2>
These are instance that have greater network priority when transferring data to/from EBS.

Instance are EBS-optimised by default for C4, M4, and D2 types. But for the others there is additional fees to enable this feature.

C4, M4, and D2 instances can be shutdown with no loss of data, on it's primary device, since the device is EBS rather then ephemeral. You can even detach it from one instance and attach it to another, which can be handy for things like realising you needed a C4 instance but currently using and M4 instance.

&nbsp;

&nbsp;
<h2>AMI Images</h2>
This is a vm template. This is the amazon's equivalent to vagrant's .box file.

All instances are created from an AMI.

You can create your own AMIs using <a href="https://www.packer.io/docs/builders/amazon.html">packer</a>.

&nbsp;

&nbsp;
<h2>Security Groups</h2>
This is a collection of port numbers you group together and give it a name. e.g. you can create a group called "web-server". And this group you have whitelisted ports 22, 80, and 443.

You can then assign this to multiple instances. This is a network level security. You still also need to do instance level security, e.g. using RHEL's firewalld.

&nbsp;