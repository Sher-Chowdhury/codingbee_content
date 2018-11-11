---
ID: 507
post_title: 'AWS &#8211; Virtual Private Cloud (VPC) overview'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-virtual-private-cloud-vpc-overview
published: true
post_date: 2016-01-16 00:00:00
---
VPC is a way to group all your resources (e.g. ec2 instances) into a network, i.e. a virtual network. This means that resources in the same vpc can communicate with each other via their private ip addresses (irrespective of which subnet's their in).

Consequently when you create a new VPC, you have to specify a range of ip addresses, in the form of a CIDR block.
<p id="fwvyYdP"><a href="http://codingbee.net/wp-content/uploads/2016/02/img_56c377631ac95.png"><img class="alignnone size-full wp-image-6792 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56c377631ac95.png" alt="" /></a></p>
Note: the tenancy setting here, let's you define at the vpc level whether resources should be fixed to specific hardware.

For example a cidr block of 10.0.0.0/16 equates to an IP range of:
<p id="AfRIWjl"><a href="http://codingbee.net/wp-content/uploads/2016/02/img_56c3784800f9e.png"><img class="alignnone size-full wp-image-6793 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56c3784800f9e.png" alt="" /></a></p>
Source: <a href="http://www.subnet-calculator.com/cidr.php" rel="nofollow">subnet-calculator</a>

Note, the range you pick can't overlap with the range of one of your other VPCs.

Within this VPC range, you can break down your range into smaller ranges, called subnets.

When you create a new resource, e.g. instance, e.g. an EC2 instance, you will get prompted to provide what vpc it should be created in followed by which subnet it is created in:
<p id="SjpnKKm"><a href="http://codingbee.net/wp-content/uploads/2016/02/img_56c37accbbccf.png"><img class="alignnone size-full wp-image-6794 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56c37accbbccf.png" alt="" /></a></p>
Once you have selected the subnet, and started the launch, your instance will get an IP address  assigned to it that's within the subnet range. This address is called the "private IP address".

Note: Private IP address are only unique inside your aws account.

You can also assign a public ip address to your vm, so that your instance ends up with 2 ip address, a public ip address, and a private ip address.

The public address is unique across the internet and is used to access your instance form the internet, whereas the private address is used by your other aws resource to connect to eachother.

Your resources can communicate with eachother via public adddresses as well, but it is better to use private ip address for performance reasons and security reasons.

&nbsp;
<h2>Public and Private Subnets</h2>
You have the option to attach an "Internet gateway" to your subnet. A subnet that has an internet gateway attached to it, is referred to as a <strong>public subnet</strong>. Whereas a <strong>private subnet</strong> is a subnet that doesn't have an internet gateway attached it. Also you attach internet gateway to a subnet via the subnet's  <strong>route table</strong>.

Before you can associate an Internet Gateway to a a vpc's subnet. You need to first associate the internet gateway to the vpc itself. You can only associate one Internet gateway to a VPC. Onc'e that's done, you can then associate this Internet Gateway to one or more of the VPC's route table, which turns them into "<strong>public route tables</strong>". You can attach a route table to a subnet. Note, all subnets must have exactly one route table, you can't have more than that, or less than that. If you attach a public route table  to a subnet, then that subnet becomes a public subnet. However if you want to ssh into an instance in this public subnet, then you also need to meet the following conditions:
<ul>
	<li>Necessary firewalls are open at the Network ACL level, so public traffic can enter the public subnet on the allowed port numbers.</li>
	<li>Necessary firewalls are open at the security group level</li>
	<li>instance has a public ip or elastic public ip address attache to it</li>
	<li>Instance level: necessary firewalld allow rules are in place</li>
	<li>Instance level: necessary services, e.g. httpd, ssh, ..etc are listening on the appropriate ports.</li>
</ul>
&nbsp;

If you want to ssh into an ec2 instance (from your home laptop) then your instance must be inside a public subnet and have a public ip address assigned to it.

If you have to use a private subnet, for security reasons, then you can still ssh into your instance by one of the following ways:
<ul>
	<li>ssh into another (publically accessible) ec2 instance that happens to reside in the same vpc, and then ssh into your  target instance, using the private IP address. This middle instance can be thought of as a <a href="http://codingbee.net/uncategorized/setting-up-bastion-hosts-in-aws-and-connect-to-ec2-instance-using-tags/">bastion host</a>.</li>
	<li>set up a vpn, so that your home laptop effectively is a member of the vpc, i.e. it is already part of the internal network.</li>
</ul>
&nbsp;
<h2></h2>
<h2>Virtual Private Network (VPN)</h2>
With a VPC, it is possible to connect your home router to the vpc, so that it appears like just another subnet in your vpc. Doing this essentially ends up creating a virtual private network.

To create a VPN, you need to attach a <a href="http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_VPN.html" rel="nofollow">Virtual Private Gateway</a> to your VPC. Therefore the only main gateways into a vpc is the Virtual Private Gateaway, or a Internet Gateway, since you can only have one Internet Gateway attached to a VPC, see diagram in:

http://blog.celingest.com/en/2013/04/19/aws-virtual-private-cloud-vpc-security/

&nbsp;
<h2>Benefits of VPC</h2>
<ul>
	<li>Good way to organise your resources  inside subnets. It's a bit like how you organise files into folders</li>
	<li>You can allow resources from different subnets to communicate with eachother by configuring route tables</li>
	<li>You can control which subnet's resources have internet access, by setting up public subnets.</li>
	<li>You can set which subnets are private, which improves security</li>
	<li>we can   make our home routeer part of the vpc, i.e. create a vpn</li>
	<li>You can improve security by using instnace security groups</li>
</ul>
&nbsp;
<h2>The Default VPC</h2>
A VPC can be a bit tricky to setup for a a begginer. That's why a newly created AWS account comes with a default vpc created for you.

The default vpc comes with a several subnets already created for you, and each of these subnets, are public subnets, which makes life easier for beginners.

New instances created inside the default vpc automatically gets assigned with 2 ip address, a public ip address and a private ip address.

&nbsp;
<h2>Elastic Network Interface</h2>
A normal linux vm, has network interfaces, e.g. eth0.

However instance inside a vpc comes with <a href="http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html" rel="nofollow">Elastic Network Interfaces</a>  (ENI)  instead. These ENI's only have private ip addresses associated with them. Traffic going to/from an instance's public ip address is actually rerouted (behind the scenes) through to the private ip address. Therefore public IP addresses are not actually attached to any network instances, that explains why public ip addresses doesn't appear when you do an "ip addr show":

&nbsp;
<pre>[root@ip-10-0-0-159 ~]# ip addr show
1: lo: &lt;LOOPBACK,UP,LOWER_UP&gt; mtu 65536 qdisc noqueue state UNKNOWN
 link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
 inet 127.0.0.1/8 scope host lo
 valid_lft forever preferred_lft forever
 inet6 ::1/128 scope host
 valid_lft forever preferred_lft forever
2: eth0: &lt;BROADCAST,MULTICAST,UP,LOWER_UP&gt; mtu 9001 qdisc pfifo_fast state UP qlen 1000
 link/ether 0e:a6:d2:74:06:57 brd ff:ff:ff:ff:ff:ff
 inet <span style="color: #ff6600;"><strong>10.0.0.159</strong></span>/24 brd 10.0.0.255 scope global dynamic eth0
 valid_lft 3191sec preferred_lft 3191sec
 inet6 fe80::ca6:d2ff:fe74:657/64 scope link
 valid_lft forever preferred_lft forever
</pre>
Here we only see the private ip address.

&nbsp;

This auto-rerouting of public ip address to the private ip address is referred to as <strong>Network Address Translation (NAT)</strong>.

&nbsp;

&nbsp;
<h2>Default VPC limits</h2>
Each aws account comes with the following limits, all of which can be increased by sending a request to aws:
<ul>
	<li>5 VPCs per region</li>
	<li>5 internet gateways per region, since you are only allowed one internet gateway per VPC, this means that all public subnets in the same vpc must all have the same internet gateway attached. Note, that a</li>
	<li>50 VPN connections per region</li>
	<li>50 customer gateways per region - customer gateways are used to set up VPN connections</li>
	<li>200 route tables per region</li>
	<li>50 entries per route table</li>
	<li>5 Elastic IP Addresses</li>
	<li>100 security groups</li>
	<li>50 rules per security groups</li>
	<li>5 security groups per network interface</li>
</ul>
&nbsp;

&nbsp;

&nbsp;

&nbsp;

Inside this vpc, it will get assigned with an ip address that's fall within this range.

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

When creating a new resource, e.g. ec2 instance, you'll get prompted to specify what vpc to create it in.

The main advantage of vpc is that resources that are created in a single vpc will be part of the same network and can communicate internally without any further netowrking configuration.

VPC themselve does not cost anything, you only pay for the costs of the resources that a vpc houses.

&nbsp;

Here's an overview example:

&nbsp;

<a href="http://codingbee.net/wp-content/uploads/2016/01/architecture_linux.png" rel="attachment wp-att-6421"><img class="alignnone size-full wp-image-6421" src="http://codingbee.net/wp-content/uploads/2016/01/architecture_linux.png" alt="" width="603" height="436" /></a>

&nbsp;

Source: <a href="http://docs.aws.amazon.com/gettingstarted/latest/wah-linux/web-app-hosting-intro.html">Amazon</a>

When creating VPC via the aws web console, you need to provide just the following info:
<p id="fCDPPuH"><a href="http://codingbee.net/wp-content/uploads/2016/02/img_56c0e222b3120.png"><img class="alignnone size-full wp-image-6789 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56c0e222b3120.png" alt="" /></a></p>
Note: you can only attach one Internet Gateway to a vpc at a time.

&nbsp;

&nbsp;
<h2>DNS server</h2>
By default each VPC that you create comes with a dns server. This dns server assigns hostnames to all the hosts that are created inside your VPC:

&nbsp;
<p id="pqYxhRJ"><a href="http://codingbee.net/wp-content/uploads/2016/02/img_56c617798cdd5.png"><img class="alignnone size-full wp-image-6818 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56c617798cdd5.png" alt="" /></a></p>
This means the that an instance's fqdn looks like:
<pre>[centos@ip-10-0-0-159 ~]$ hostnamectl
 Static hostname: <strong>ip-10-0-0-159.ec2.internal</strong>
 Icon name: computer
 Chassis: n/a
 Machine ID: 0af04d3c78a943ae8f3cc26602e374f2
 Boot ID: 6fd874e6b35b40568f53c7c0540259cd
 Operating System: CentOS Linux 7 (Core)
 CPE OS Name: cpe:/o:centos:centos:7
 Kernel: Linux 3.10.0-229.14.1.el7.x86_64
 Architecture: x86_64
[centos@ip-10-0-0-159 ~]$</pre>
&nbsp;

&nbsp;

If you don't want to use this default dns server, and instead you have your own dns server, then you can use  this by creating a custom DHCP option set:

&nbsp;
<p id="NVDYsMa"><a href="http://codingbee.net/wp-content/uploads/2016/02/img_56c6189b66d19.png"><img class="alignnone size-full wp-image-6819 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56c6189b66d19.png" alt="" /></a></p>
&nbsp;
<h2>Route tables</h2>
A <a href="http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Route_Tables.html" rel="nofollow">route table</a> helps your vpc to traffic.

When you create a VPC, a route table automatically get's created along with it and this route table is the vpc's "main" route table.
<p id="oqUkWvS"><a href="http://codingbee.net/wp-content/uploads/2016/02/img_56c61d53cb191.png"><img class="alignnone size-full wp-image-6821 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56c61d53cb191.png" alt="" /></a></p>
&nbsp;

By being "main" it means that this route table get's automatically attached to each private/public subnet that get's created in the vpc. This Main route table has an inital route rule called "local" as shown above. This rule makes it makes it possible for your VPC's resources to communicate with each other via the private IP, irrespective of which private/public subnets the resources are in.

You can't delete this "local" route rulem and any new route tables that you create will always have this vpc wide "local" route rule.

Note: A subnet always has exactly one route table attached to it, it can't have more than that, or less than that, this means that you can switch route tables.

You can only attach one Internet Gateway to a route table. In doing so, any private subnet associated with this route table, become public subnets.

Note, you can only have one active internet gateway per vpc.

&nbsp;

<a href="http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Introduction.html" rel="nofollow">Amazon vpc</a>