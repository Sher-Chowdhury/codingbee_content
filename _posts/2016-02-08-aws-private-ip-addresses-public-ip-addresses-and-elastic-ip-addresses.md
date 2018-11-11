---
ID: 541
post_title: 'AWS &#8211; Private IP Addresses, Public IP addresses, and Elastic IP Addresses'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-private-ip-addresses-public-ip-addresses-and-elastic-ip-addresses
published: true
post_date: 2016-02-08 00:00:00
---
A public IP address is not a static IP address, i.e. it will change if you reboot your EC2 instance

&nbsp;

Elastic IP Addresses on the other hand is persistant. you can assign to one instance, or at a later date reassign it to a different instance.

Inside a vpc we can have to types of subnets:

&nbsp;
<ul>
	<li><strong>Private subnets</strong> - This is a subnet that does not have an internet gateway attached to it. This means an instance  can't access the internet via this type of subnet. This address is persitant and will survive a reboot.</li>
	<li><strong>Public subnets</strong> - This is a subnet that does have an internet gateway attached to it.</li>
</ul>
&nbsp;

In order for an instance to have internet access, it first needs to be in a vpc that contains a Public Subnet. The next thing it needs is either a public address or elastic ip address.

Attaching a Public/Elastic IP address to an instance is a bit like assigning an IP address to an instance's network card.

Elastic IP address works by associating itself to a public IP address.

&nbsp;

It is possible for an instance to have a public/elastic ip address, but is a vpc that only contains a private subnet. In this situation, the instance will still not have access to the internet.

Therefore for an EC2 instance to have internet access, that instance needs to have a public/elastic IP address assigned to it, and the instance needs to also reside inside a public subnet.

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

http://aws.amazon.com/ec2/pricing/#Elastic_IP_Addresses

&nbsp;

http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html