---
ID: 573
post_title: 'AWS &#8211; Natively available AWS features for enhancing security'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-natively-available-aws-features-for-enhancing-security
published: true
post_date: 2016-05-02 00:00:00
---
AWS offers a bunch of natively security features that we can use to enhance security:
<ul>
 	<li>AWS API access security - via api keys</li>
 	<li>buitin vpc firewalls - private and public subnets. Encourages us to use private subnets whenever possible</li>
 	<li>IAM - only authenticated users and apps are granted access privileges</li>
 	<li>MFA - multifactor authentication - must use android phone as part of login process</li>
 	<li>Encrypt data stores - e.g. the Encrypted EBS feature, also s3 encryption</li>
 	<li>AWS direct connect - ISP's routes AWS traffic straight to AWS AZs, without going through the rest of the internet</li>
 	<li>Monitoring aws api usage - i.e. cloudtrail. Keeps track of user activities</li>
 	<li>AWS config - Lets you compare point-in-time snapshot view of how your infrastructure has changed over time. This is useful for example if you want to see what ec2 instances have been created/stopped/terminated a 5 days go, compared to today.   Each point-in-time snapshot is documented in json format</li>
 	<li><a href="https://aws.amazon.com/kms/" rel="nofollow">Key management service</a> - A place to store your private keys</li>
 	<li>A prart of AWS that's isolated from the rest of AWS in order for use by governments, aka govcloud. This part has industry standards to satisfy goverment security grequeiments.</li>
 	<li><a href="https://aws.amazon.com/cloudhsm/" rel="nofollow">CloudHSM </a>- This is a Hardware Security Module (HSM) for hardware based encryption</li>
 	<li><a href="https://aws.amazon.com/premiumsupport/trustedadvisor/" rel="nofollow">AWS Trusted Advisor</a> - This is an AWS support service (which is available as part of premiere support) where an AWS specialist reviews your infrastructure to identify any ways to improves your AWS setup's:
<ul>
 	<li>Cost efficiency</li>
 	<li>security</li>
 	<li>High Availability and  fault tolerance</li>
 	<li>performance</li>
</ul>
</li>
</ul>
&nbsp;

&nbsp;