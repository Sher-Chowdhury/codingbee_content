---
ID: 520
post_title: 'AWS &#8211; Simple Notification Service (SNS)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-simple-notification-service-sns
published: true
post_date: 2016-01-16 00:00:00
---
This is AWS's  push notification service.

&nbsp;

SNS supports the following target destinations (aka endpoints):
<ul>
 	<li>Amazon SQS</li>
 	<li>Email</li>
 	<li>Email-json</li>
 	<li>SMS</li>
 	<li>HTTPS</li>
 	<li>HTTP</li>
 	<li>Application - e.g. Android App, such as a whatsapp notification</li>
 	<li>AWS Lambda</li>
</ul>
&nbsp;

A lot of AWS own services internally makes use of SNS as well for example  S3 RRS makes use of this to send notifications when an object (e.g. file) gets lost or corrupted.

&nbsp;

&nbsp;

The way SNS works is that in the SNS section of the aws console, create create a new SNS resource called 'topic'. You can think of each topic as a noticeboard, or a forum thread. You can then add subscribed to this topic (e.g. by providing your email address, mobile number,...etc), so that when new updates arrives to the topic, sns will send out messages to all the subscriptions attached to the topic.

This could be a good way to regenerate new puppet certificates, if existing S3 stored puppet certificates gets corrupted.

SNS is a good way to loosely decouple your architecture.

&nbsp;

You publish new messages to your topic by writing code to send the messages to your SNS topic. Probably done via the use of

&nbsp;

http://docs.aws.amazon.com/sns/latest/dg/GettingStarted.html

https://aws.amazon.com/sns/