---
ID: 537
post_title: 'AWS &#8211; Monitor all user AWS activities (Cloudtrail)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-monitor-all-user-aws-activities-cloudtrail
published: true
post_date: 2016-02-06 00:00:00
---
Logging activities and events are important. There are two kinds of things you want to create logs for:
<ul>
	<li>logging of capacity of EC2 instance, e.g. cpu utilisation - This is done by the  Cloudwatch service.</li>
	<li>AWS interaction logging. E.g. which deleted which EC2 instance - this kind of logging is done by the Cloudtrail service.</li>
</ul>
It's good practice to log all the activities authorized AWS users are doing on AWS. The users can interact with AWS either via the web console or the aws cli, in both cases all activities will get logged by Cloudtrail, if you enable it.

Cloudtrail tightly integrates with other aws services. In particular Cloudtrail stores all the logs that it generates, in its own S3 bucket. You get to choose the name of your S3 bucket as part of activating Cloudtrail.  You can also set up SNS alerts when particular events occur, e.g. someone deletes an EC2 instance. However these event level notifications are done in conjunction with cloudwatch.

You can also link up Cloudtrail with the Simple Notication Service (SNS) service. In this scenario you get set up email notifications every time a new log file is sent to S3. Each of these log entry specifies, which user did what to which resource (e.g. EC2 instance id), and when. It also gives the user's source ip address, and what region the event occurred in.

&nbsp;

&nbsp;

&nbsp;