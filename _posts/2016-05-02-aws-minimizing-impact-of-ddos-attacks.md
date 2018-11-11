---
ID: 574
post_title: 'AWS &#8211; Minimizing impact of DDOS attacks'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-minimizing-impact-of-ddos-attacks
published: true
post_date: 2016-05-02 00:00:00
---
We can limit DDOS attacks in the following ways:
<ul>
 	<li>identify ip range of ddos attacks and block it at the Network ACL level. Alternatively could do this at the Security Group Level, but it's quicker at the Network ACL level.</li>
 	<li>Install DDOS prevention software on our EC2 instances that will monitor for DDOS attacks and filter them out.</li>
</ul>
&nbsp;

AWS minimizes impact by:
<ul>
 	<li>use of CloudFront, which can absorb most of the impact. Hence the edge collections takes on the main brunt of the attack</li>
 	<li>If ddos against a static website that's hosted on S3, then S3 will absorb this impact.</li>
 	<li>port scanning (using the nmap command) is disabled by default in AWS (even port scanning between EC2 instances that are inside the same VPC). If you want to enable port scanning, then you need to contact AWS for permission</li>
 	<li>AWS has enabled ingress filtering on all incoming requests.</li>
</ul>