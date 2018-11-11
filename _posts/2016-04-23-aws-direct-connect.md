---
ID: 566
post_title: 'AWS &#8211; Direct Connect'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-direct-connect
published: true
post_date: 2016-04-23 00:00:00
---
Some Internet Service Providers can connect your on premise devices directly to aws AZ without being rerouted via the rest of the internet.
<ul>
 	<li>this results in faster connection</li>
 	<li>more stable connection</li>
 	<li>reduced latency</li>
 	<li>No need to go via the public internet</li>
 	<li>better security</li>
 	<li>No need to have any special hardware on-site</li>
 	<li>aws charges less for data traffic going to from aws via direct connect, rather than via the internet</li>
 	<li>It gives you dedicated private network connection. I.e. you can connect to your ec2 instances via it's private ip addresses. Previously we saw that this is possible by setting up vpn, however with Direct Connect, you don't need to set up connection. This is achieved by your onsite devices connecting to your vpc via a Virtual Gateway that's attached to your vpc. To achieve this you need to setup Virtual Interfaces (vifs) on your onsite devices.</li>
</ul>
&nbsp;

private virtual interface: this is something that has to be attached to your vpc to enable direct connect connections to your ec2 instances using private ip addresses. you can think of ec2 private ip addresses as private endpoints. Each vpc must have it's own vif in order to connect to it via direct connect.

However some aws services, e.g. rds and s3 by default  have public endpoints. So you can't using private vifs with things like S3. Theres 2 ways to overcome this:
<ul>
 	<li>you can actually set up private endpoints for these aws services, which will then make it possible to continue using private vifs</li>
 	<li>Use "Public VIFS", this is essentially private vifs except some extra rerouting is done on the AWS region side to reroute the traffic from aws direct connect, to the public endpoints.</li>
</ul>
You can also attach Public VIFS to your vpc too. This would then let you access your ec2 instances via their public ip addresses too, but still going through via direct connect.

public VIFS makes use of public CIDR block range, but traffic is still routed via dedicated network and hardware, due to direct connect.

&nbsp;

In order to use direct connect, your ISP needs to establish a connection from your site to it's internal traffic network. This connection is referred to as a cross-connect, aka cross network connection.

&nbsp;

Direct Connect, by design, can only connect to a single AWS region.

&nbsp;

https://aws.amazon.com/directconnect/faqs/

&nbsp;