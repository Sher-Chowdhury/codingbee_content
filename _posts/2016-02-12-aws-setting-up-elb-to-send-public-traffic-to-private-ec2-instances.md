---
ID: 546
post_title: 'AWS &#8211; Setting up ELB to send public traffic to Private EC2 instances'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-setting-up-elb-to-send-public-traffic-to-private-ec2-instances
published: true
post_date: 2016-02-12 00:00:00
---
Here are the steps:
<ol>
 	<li>Create a vpc (which also creates a new route table behind the scenes)</li>
 	<li>Create 2 subnets inside this vpc. These subnets also needs to be in the same availability zone as the web-server instance, which by default both subnets ends up inheriting the vpc's route table. We'll call them <em>public-subnet</em> and <em>private-subnet</em>.</li>
 	<li>Create an "Internet Gateway" and attach it to the vpc's routing table. This ends up making both <em>public-subnet</em> and <em>private-subnet, </em>public.  Later on we will set  the subnet, <em>private-subnet</em><em>,</em> to a private subnet<em>.</em></li>
 	<li>attach the Internet Gateway to the route table, configure it to accept traffic from an IP address</li>
 	<li>Create an EC2 instance inside the vpc (via dropdown list), and attach the subnet, private-subnet (via dropdown list). Also assign a public ip address</li>
 	<li>Log into this EC2 instance using the public ip address, it is still accessible via the public address since private-subnet is still actually private</li>
 	<li>Install httpd, disable firewalld, start httpd service inside the instance</li>
 	<li>check that you can access the instance homepage via the ip address.</li>
 	<li>In AWS web console's ec2 section, create an ELB, attach this ELB to the public-subnet, attach your ec2 to this ELB instance.</li>
 	<li>Check you can access the instance's homepage, this time via the ELB's url, the ip address should also work too.</li>
 	<li>Now go back into the VPC section and create a new route table, call it "private-route-table", don't attach an Internet Gateway to this.</li>
 	<li>Switch the <em>private-subnet's</em>  route table to this one.</li>
 	<li>The homepage should no longer be accessible via the ip address, and now only works via the elb's url. Also you can't no longer ssh into the instance. You can use the elb's url to do this, since the elb is designed to distribute traffic (also why you can't open ssh port when creating elb, only http and https). To now ssh into your instance you will need to do it in of the following ways:
<ol>
 	<li>ssh to another instance that is publicly accessible, and shares a subnet with the target instance, then ssh into that.</li>
 	<li>use <a href="https://aws.amazon.com/directconnect/" rel="nofollow">aws direct connect</a></li>
 	<li>set up a vpn</li>
</ol>
</li>
</ol>
&nbsp;
<h2>vpc and subnets</h2>
To create a vpc, you need to provide:

<a href="http://codingbee.net/wp-content/uploads/2016/02/dk8KXGB.png" rel="attachment wp-att-6691"><img class="alignnone size-full wp-image-6691" src="http://codingbee.net/wp-content/uploads/2016/02/dk8KXGB.png" alt="" width="1126" height="766" /></a>

A vpc is essentially a range of ip addresses.

&nbsp;

You can break this range, into smaller ranges, by creating subnets.

<a href="http://codingbee.net/wp-content/uploads/2016/02/RT10GyU.png" rel="attachment wp-att-6692"><img class="alignnone size-full wp-image-6692" src="http://codingbee.net/wp-content/uploads/2016/02/RT10GyU.png" alt="" width="1089" height="636" /></a>

Where:

<a href="http://codingbee.net/wp-content/uploads/2016/02/XGlk7hw.png" rel="attachment wp-att-6693"><img class="alignnone size-full wp-image-6693" src="http://codingbee.net/wp-content/uploads/2016/02/XGlk7hw.png" alt="" width="947" height="566" /></a>

&nbsp;

Let's assume in this scenario, that you only plan to set this up for a single private EC2 instance:

&nbsp;
<ol>
 	<li>Create a vpc, with the following info:
<ol>
 	<li>name=vpc1</li>
 	<li>CIDR block range=10.0.0.16</li>
 	<li>Tenancy = default</li>
</ol>
</li>
 	<li>Create a subnet in the "London" AZ. Note this AZ will be where our private EC2 instance will reside. A subnet is attached to an AZ. Here are the details you need to provide:
<ol>
 	<li>  subnet name="public-subnet"</li>
 	<li>vpc name="vpc1"</li>
 	<li>AZ=london</li>
 	<li>CIDR Block=10.0.1.0/24</li>
</ol>
</li>
 	<li>Create a private subnet with the London AZ, with the following details:
<ol>
 	<li>subnet name = "private-subnet"</li>
 	<li>vpc name="vpc1"</li>
 	<li>AZ=London</li>
 	<li>CIDR block=10.0.2.0/24</li>
</ol>
</li>
</ol>
&nbsp;

&nbsp;

A Public subnet must exist in the vpc, on the same AZ as the private subnet that hosts the private ec2 instance. That's because a public ELB behind the scenes builds EC2 instance on a public subnet, and then reroutes traffic to private ec2 instances. The reason that this public subnet must be in the same AZ as the private subnet, is that it improves performance.

Note: when creating an ELB, you will be prompted to select at least 2 subnets for High Availability purposes. That's why it's best to create another public subnet in a different AZ to achieve this. Although it's unlikely this second pubsubnet get's used.

&nbsp;

&nbsp;

&nbsp;