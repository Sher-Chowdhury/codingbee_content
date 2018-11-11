---
ID: 577
post_title: 'AWS &#8211; Cloudwatch related security features'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-cloudwatch-related-security-features
published: true
post_date: 2016-05-02 00:00:00
---
Cloudwatch related API requests are signed with HMAC-SHA1signature from the request and the the user's private key

Cloudwatch's (sdk) API is only accessible via https, not http, i.e. it is encrypted with ssl

An IAM user can only access cloudwatch if they are given access via IAM

You can configure cloudtrail to send notifications to SNS, which in turn sends notifications to cloudwatch, to take particular actions when something happens, e.g. restrict a certain user's permission if they do something unwanted.

&nbsp;

You can also get cloudwatch to send all of your EC2 instance system logs to your cloudwatch in real time:

http://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/WhatIsCloudWatchLogs.html

&nbsp;