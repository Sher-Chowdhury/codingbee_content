---
ID: 542
post_title: 'AWS &#8211; Security Groups and Network ACLs (firewalls)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-security-groups-and-network-acls-firewalls
published: true
post_date: 2016-02-09 00:00:00
---
You can control data traffic flowing to/from your resources via security groups. Or at the higher subnet level, via Network ACLs.
<h2></h2>
<h2>Security Groups</h2>
When you create a new instance, then by default all data traffic that are attempting to reach the EC2 instance (across all ports) are denied. I think the only exception is the ssh port, port 22.

You can apply individual/collection-of firewall rules to an instance, in the form of a "security group".

Security groups are a grouping of firewall rules, that you can assign to an EC2 instance.

Security groups are things that exist at the vpc level. Therefore once you create a security group, you can then assign that security group to any EC2 instances that exists in that vpc.

Security Group firewall rules are <strong>stateful</strong>, meaning that if you allow incoming traffic for a given ip-range/security-group and port number, then the security group will allow outbound traffic too, via the same security group's firewall rule.

For each AWS account, you can have up to 5 vpc. And for each vpc, you can create up to 100 security groups. However all of these are arbitrary limits and they can be increased by submitting a request to aws.

Each security can have up to 50 firewall rules.

You can assign up to 5 security groups to an EC2 instance.

firewall rules can also specify source ip addresses, or an ip address range.

Two instances can speak to each other, as long as they both are assigned with the same security group, and you have specified a compatible traffic source.

Every time a new VPC is created, a "default security group" is automatically created as well. This security group actually has the name "default":
<p id="xdkhQcJ"><a href="http://codingbee.net/wp-content/uploads/2016/02/img_56c4c9ffe921b.png"><img class="alignnone size-full wp-image-6805 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56c4c9ffe921b.png" alt="" /></a></p>
By default the default security is configured to allow all the vpc's resources to communicate with each other on all ports.   This essentially means any traffic coming from that security group, is allowed. Note, the source field can be one of the following:
<ul>
	<li>ip address</li>
	<li>An ip address range</li>
	<li>A security group's id</li>
</ul>
This Default Security Group is automatically gets assigned to a an instance, if you don't assign different security group during EC2 instance creation time.

&nbsp;
<h2>Network ACLS</h2>
Network ACLs are similar to security groups, except that they operate at a subnet level, i.e. it can block traffic that is trying to enter a subnet itself.

Therefore you attach security groups to EC2 instances, whereas you attach Network ACLs to subnets.

Another big difference is that that in Security groups you specify "ALLOW" rules only, otherwise everything is implicitly denied. However in Network ACLS, you can specify both ALLOW and DENY rules.

&nbsp;

Also for Network ACLs, each rule has a number attached. This number is referred to as the rule number. Number=1 is top priority, and the bigger the number, the lower the priority. This is useful if you create 2 conflicting firewall rules. The firewall rule with the rule number closer to one will override the other conflicting firewall rules.

&nbsp;

However there is a special firewall rule, which has a rule number of "*". This is a catch-all rule that get's triggered if no existing firewall rules is triggered.
<p id="jgNkpPl"><a href="http://codingbee.net/wp-content/uploads/2016/02/img_56c4c79520211.png"><img class="alignnone size-full wp-image-6802 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56c4c79520211.png" alt="" /></a></p>
This catchall '*' rule essentially sets the inbound Network ACL default to deny. However in this case, the rule 100 always get's triggered, so this catch-all rule never comes into play.

&nbsp;

&nbsp;

Network ACL's are <strong>stateless</strong>, which means that you have to create inbound and outbound firewall rules, to allow two way traffic.

&nbsp;

&nbsp;

http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html