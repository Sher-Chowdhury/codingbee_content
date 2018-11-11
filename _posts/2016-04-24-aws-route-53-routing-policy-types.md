---
ID: 570
post_title: 'AWS &#8211; Route 53 routing policy types'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-route-53-routing-policy-types
published: true
post_date: 2016-04-24 00:00:00
---
In route53 you have multiple entries with the same url (aka url). In fact you have to create multiple entries with the same name in order to take advantage of the various routing policies. Here are the available routing policies:
<ul>
 	<li>Simple</li>
 	<li>Weighted</li>
 	<li>Latency</li>
 	<li>Failover</li>
 	<li>Geolocation</li>
</ul>
We have already covered Failover.

&nbsp;
<h2>Latency based routing</h2>
One thing you can do is set up the exact same VPC in 2 different regions.

You can then configure route 53 to route traffic to the VPC that is going to be the first responder to a given source's request. This usually means that the vpc that is geographically closer to the requester's location, will end up handling the request.

This indirectly means that if one vpc goes down, all traffic will failover to the vpc that is still active, since it essentially becomes the first responder.

&nbsp;

To set this up, you select "latency" from the route53 entry's "routing policy" dropdown list, and then specify a region from the down list. You then create another record for the same url again, and again choose latency. But this time specify a different region from the dropdown list. I think specifying region from the dropdown list helps route53 to route traffic faster, rather than route53 taking more time to figure out which region the corresponding ip address belongs too.

When a request is then made, route53 will ping both vpc's and the vpc that responds fastest ends up becoming the responder.
<h2>Weighted based routing</h2>
This option lets you do load balancing at the route 53 level.

Here you set a value for the "weight". This value can range from 1-100. If you set this to '1' in both entries, then route53 will send traffic to both endpoints evenly. If the 2nd entry instead has '10', then for every 11 requests, the first entry will get 1, and the second entry will get 10.

This is a useful way to gradually introduce replacement for existing architecture. E.g. existing architecture is given weight of 100, and new architecture given weight of 1. Then over the course of days and weeks, you gradually decrease the weighting value of the existing architecture, and increase the weight value of the new architecture. This is also a good way to test whether your new architecture can handle the load.

&nbsp;
<h2>Geolocation based routing</h2>
This option lets you route traffic to various endpoints based on the requester's country of origin. Hence you select a country from a massive dropdown list. This is useful if you want to direct USA customers to USA version of your website, which shows all prices in US dollar, if you run an online shop.

&nbsp;

&nbsp;

&nbsp;

&nbsp;