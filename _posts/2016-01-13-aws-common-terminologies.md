---
ID: 502
post_title: 'AWS &#8211; Common terminologies'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-common-terminologies
published: true
post_date: 2016-01-13 00:00:00
---
AWS in it's physical form is made up of a large number of data centers across the world. These data centers are independent of each other, meaning that if one data center goes down, it will not impact the others.

A data center is essentially a really big build that houses hundreds/thousands of physical servers. In AWS these data centres are referred to as <strong>Availability Zones</strong>.  These Availability Zones are organised into groups, based on geographical proximity, and each of these groups are referred to as  <strong>regions</strong>.

Making use of all the <strong>Availability Zones</strong> in a region  has several advantages:
<ul>
	<li>low latency - if a AZ is close to an end user.</li>
	<li>fault tolerance</li>
</ul>
&nbsp;

The AWS console is designed to only interact with one region at a time, you can navigate between regions using the console.

Some AWS services are not restricted to a region, instead they operate worldwide, For example, "Edge Locations"

<strong>Edge Location</strong> - This is an AWS datacenter which is primarily used for caching content, so that it can deliver content to the requester more quickly. E.g. Cloudfront, which is a Content Delivery Network (CDN). These data centers are used for covering parts of the world that are not covered by any AZs. Hence Edge Locations are data centres that are primarily used for caching static content, e.g. html+css files, audio files, video files, ...etc.

scalability - able to add/remove resources to a service as and when needed. e.g. logical volumes are scalable compared to using a simple partition. Scalability has the following advantages:
<ul>
	<li>Resilient - automatically add more cpu resources when demand increases.</li>
	<li>Efficient</li>
	<li>Cost effective - allocate just enough amount of resources to meet demands.</li>
</ul>
Using vm's is an example of scalability.

<strong>Fault Tolerant</strong> (aka failovers, or redundancies) - this means that the your system carries on working even if there is a service failure, e.g. when an AZ goes down. E.g. if one server fails, then there is another server that will take over.

<strong>Elasticity</strong> - Pretty much means that same thing as scalability, i.e. can automatically scale up and scale down. This means we pay end up paying for only what we need.

Some amazon resources have elasticity built in, e.g. Amazon S3, whereas others are not, e.g. EC2.

Ther are 3 main types of scaling:
<ul>
	<li><strong>proactive Cycle Scaling</strong>: automatically start up and shutdown vms during peak periods, e.g. 9am-5pm mon-fri.</li>
	<li><strong>proactive Event-Based Scaling</strong>: Can automatiically scale in anticapation peaks caused by certain events. E.g. black-friday, boxing day, half price sale days.</li>
	<li><strong>Auto-Scaling Based on Demand</strong>: scale up based on things like cpu, ram, bandwidth, reaches a certain predefined upper limit, e.g. 80%. This type of scaling has small delay when scaling up/down. I.e. it can take a couple of minutes for new EC2 register and become part of the applicaiton. Hence, end users may experience minor outages, if there is a very sharp peak. Another way to scale is based on jobs in the queue (e.g. queueing jobs in the Jenkins setup)</li>
</ul>