---
ID: 548
post_title: 'AWS &#8211; NAT Instances'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-nat-instances
published: true
post_date: 2016-02-18 00:00:00
---
If you have an ec2 instance that is attached to a private subnet only, then it won't have internet access. That's because by definition, the routing table that is associated with the private subnet, doesn't have an entry for routing traffic to/from an internet gateway.

For security reasons, this is a good thing, if the resources does not provide a service that requires direct access. However this is also a problem, because you can't use commands like yum which requires internet access.

You can overcome this problem by creating a <a href="http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_NAT_Instance.html" rel="nofollow">NAT instance</a>.  A NAT instance allows instances that are on the private subnet to initiate outbound traffic to the internet, and thereby establishing a connection session which will then allow inbound traffic for the duration fo the session. It's a bit like a temporary tunnel that is created by the private instance which is closed again as soon as the private instance received the traffic it requested for.

A NAT instance is essentially a special type of EC2 instance. A NAT instance is an EC2 instance with the following characteristics:
<ul>
	<li>A NAT instance is an EC2 isntace that is created from AMI that includes the string <strong>amzn-ami-vpc-nat</strong> in it's name. You can find these AMI's by searching in the Community AMIs sections.</li>
	<li>The NAT instance's type needs to be quite big in order to have enough network data transfer capacity.</li>
	<li>NAT instance must be connected to a public subnet and must have a public IP address (or Elastic Public IP Address). I.e. the NAT instance must have internet access.</li>
	<li>The NAT instance should have a security group attached to it called, "NATSG". This name is not manadatory but is recommended by AWS</li>
	<li>NATSG needs to have the following inbound firewall rules:
<ul>
	<li>SSH for all ip addresses (or try to limit this if possible)</li>
	<li>HTTP limited to the private subnet's ip range - That's because if you look at yum's .repo config files, you'll see baseurl is http</li>
	<li>HTTPS limited to the private subnet's ip range - That's because yum's .repo's baseurl can also be https.</li>
</ul>
</li>
	<li>The NAT Instance needs to have the <strong>source/destination checks</strong> setting disabled. Otherwise the NAT instance will terminate outbound data packets that it is not the source of. It will also terminate in bound packats that it isn't the destination of either. You can modify this setting by right clicking on your instance, and navigating to:
<p id="vpTvTeL"><a href="http://codingbee.net/wp-content/uploads/2016/04/img_570a7fab1b402.png"><img class="alignnone size-full wp-image-6897 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/04/img_570a7fab1b402.png" alt="" /></a></p>
</li>
	<li>The route table that's attached to the private subnet must have an entry that routes all other traffic (0.0.0.0) bound traffic to the NAT Instance. The "local route" will intercepts all internal traffic first. so the 0.0.0.0 will only come into play for IP ranges that are outside of the VPC IP range.</li>
</ul>
&nbsp;

The EC2 doesn't need to have a public IP address. Therefore the internet will treat the NAT instance's public IP address as the source address. When the NAT instance receives the response data traffic, it will reroute the traffic back to the EC2 instance.

&nbsp;

&nbsp;

&nbsp;