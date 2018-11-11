---
ID: 568
post_title: 'AWS &#8211; Setting up DNS failover in Route 53'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-setting-up-dns-failover-in-route-53
published: true
post_date: 2016-04-23 00:00:00
---
You can set up active and passive entries for the same url in route 53. So when route 53 discovers that the active (primary) source has become unhealthy it will failover to the passive (secondary) entry.

For example, we have a static website running on an EC2 instance. This EC2 instance is attached to an ELB. We have a route 53 alias that points to the ELB.

Now let's say we have a file called "index.html" that is stored in S3. Also let's assume that we configured S3 to act as a regular website. Now on the index.html file,  let's assume it  says something like "this website is currently down, come back later". Also let's say we created a cloudfront distribution, that used this S3 bucket as a source.

&nbsp;

Now to set up failover to the cloudfront/S3 bucket, we create 2 records in route 53. The trick is to create 2 dns entries with identical "url" name. The first one is aliased to the elb's url, and the second one is aliased to the cloudfront's url.

In both cases, you need to select "failover" from the "routing policy" dropdown list, also you leave the "Set ID" field unchanged.

For the primary route53 entry, you also need to enable the "Evaluate Target Health" radio button.

&nbsp;

&nbsp;

http://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-restricting-access-to-s3.html