---
ID: 519
post_title: 'AWS &#8211; Amazon Simple Queue Service (SQS)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-amazon-simple-queue-service-sqs
published: true
post_date: 2016-01-16 00:00:00
---
If you have incoming work requests, then you can queue them up if the existing EC2 instances are pre-occupied, rather than auto-scale up your EC2 resource.

This service works by a new message (in the form of a json file) being created by each incoming work request.  This json file get's sent to the SQS service. We then configure all our ec2 instances to poll the sqs when they are idle, and process the next json file (if any) that is in the queue.

E.g. when a user uploads a video to youtube, that video would then need to be transcoded. The youtube website could upload the video to S3 and then "publish a message" to a "topic" in SNS. SNS in turn will then send "push" notifications to all subscription, one of which can be an SQS service. The SQS service can queue all these incoming messages in json  form.  .

You can also set up auto-scaling based on the length of the queue. If the queue gets too long, then new ec2 instances are created to help cope with the demand.

SQS has built-in high availability. meaning that the same queue exists and in sync across two or more  Availability Zones.

SQS is a hosted fully managed service from AWS.   Behind the scene, SQS runs on EC2 instances, and these instance are configured to be highly, by having multiple internal SQS based instance running in different AZs inside a region. All these internal SQS instance keep the queue in sync across the region.

&nbsp;

Some key SQS characteristics:
<ul>
 	<li>Each message in the queue is up to 256KB, which is a quarter of an MB.</li>
 	<li>The message's content can be of any format, text, json, xml, yaml...etc</li>
 	<li>SQS gaurantees to deliver each message to the polling requesters at least once.</li>
 	<li>SQS tries to deliver the messages in the order SQS receives them. But can't gaurantee this order. In a lot of cases the exact ordering doesn't matter, because each message isn't really dependant each other. But if they are then maybe SQS isn't the best option.</li>
 	<li>Some SQS messages might be duplicates. Although SQS will do it's best to keep duplicates to a minimum.</li>
 	<li>By default a message stays in the queue for up to 4 days. However this limit can be increased to a maximum of 14 days. After the message exceeded the limt, AWS will automatically delete the message.</li>
</ul>
&nbsp;

Usually applications (including AWS based apps, such as sqs) are developed to send messages to the SQS queue. You then have separate application (which can be running on EC2 instances) that polls SQS's queue when they are idle, to retrieve the next message in the queue.

AWS charges for SQS in terms of API requests from the workers that are polling the SQS queue.

There are 2 main style that worker (EC2 instance) can poll an SQS for new messages in the queue:
<ul>
 	<li>Long polling  -
<ul>
 	<li>the worker connects to SQS for a predefined period of time, e.g. 10 seconds. It will pull down the entire queue of messages along with any new messages that arrived during those 10 seconds. Then it will process all the messages in the queue.</li>
 	<li>The long polling duration can be anywhere from 1 seconds to a maximum of 20 seconds. All these messages can then be taken off the queue.</li>
 	<li>PRO: AWS charges a cheaper price for long polling, because it reduces the number of api requests.</li>
 	<li>CON: If worker dies, then it will have a bigger negative impact.</li>
</ul>
</li>
 	<li>short  polling -
<ul>
 	<li>Queries for a quick list of messages in the queue. SQS has to give a quick response so only queries a subset of it's internal sqs instance to compile the queue and return that. Hence this queue is unlikely to be a full queue of messages.</li>
 	<li>PRO: worker gets a faster response from SQS</li>
 	<li>CON: AWS charges more, since there are more frequent API requests.</li>
</ul>
</li>
</ul>
&nbsp;
<h2>Types of coupling</h2>
Tightly coupled - When 2 components are not only linked but are dependent on each other in order to work. This type of coupling should be avoided.

Loosely Coupled - When 2 components are linked to eachother but not dependant. This means you can replace faulty components with minimal impact on other components. E.g. if a jenkins slave fails, then all the jobs stays in the queue until jenkins is replaced.

You can build architecture that are loosely decoupled by implementing the following into your architecture:
<ul>
 	<li>SWF (Simple Workflow Service)</li>
 	<li>SQS (Simple Queue Service)</li>
</ul>
&nbsp;

&nbsp;

&nbsp;

https://aws.amazon.com/sqs/