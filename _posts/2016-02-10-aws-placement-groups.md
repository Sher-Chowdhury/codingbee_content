---
ID: 544
post_title: 'AWS &#8211; Placement Groups'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-placement-groups
published: true
post_date: 2016-02-10 00:00:00
---
<h2>Key points</h2>
<ul>
 	<li>the name of a placement group has to be unique within your aws account.</li>
 	<li>only certain instance types can be attached to placement groups, c, g, m, i</li>
 	<li>you can't move existing instances into a placement group.</li>
</ul>
&nbsp;

A<strong> placement group</strong>  is a cluster of instances that are all in the same AZ. These instances have 10 Gbps networks and they  need to have the "<a href="https://aws.amazon.com/ec2/instance-types/#enhanced_networking" rel="nofollow">Enhanced Networking</a>" feature. You need to puts instances into a placement group if they need to have very low latency between them.

You need to use appropriate EC2 instance types to get the best use out of placement groups.

You should try create all your ec2 (placement group) instances in a single request, this is possible because when creating an instance, there is a "number of field" to fill. This inadvertantly means that all your instances must be of the same instance type.   When creating all your placement group instances in a single request,  Amazon will do it's best to create your instance on the same hardware, or at least as close as possible.

Once created, if you stop and then start an instance, then this instance will start up in the same AZ, and aws will again try to create it as close to as possible to the other placement group members.

&nbsp;

A common troubleshooting technique is to stop all your placement group instances, and start them up again. This will help aws to relocate all your instances to be as close as possible again.

&nbsp;

Placement group restrictions:
<ul>
 	<li>instances that were not originally created in a placement group, cannot be added to a placement group.</li>
 	<li>placement groups can't be combined into one. Although instances inside a placement group can communicate with outside instances.</li>
 	<li>All instances of a placement group must reside in the same AZ.</li>
 	<li>You can't have 2 or more placement group with the same name.</li>
</ul>
&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups.html