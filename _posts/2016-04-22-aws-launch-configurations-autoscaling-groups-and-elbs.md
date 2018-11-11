---
ID: 565
post_title: 'AWS &#8211; Launch Configurations, Autoscaling Groups, and ELBs'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-launch-configurations-autoscaling-groups-and-elbs
published: true
post_date: 2016-04-22 00:00:00
---
Launch Configurations - let's you specify a template for automatically create new instance, based on this presets. Therefore when creating a Launch Configuration resource you specify things like:
- AMI id
- Instance type
- Which vpc to build instances in 
- IAM role
- default storage requirements. E.g. number and size of ebs blocks to attach to new EC2 instances
- User data script
- security groups
- which ssh keys to use, to log into instances

Autoscaling Groups - this is where you specify under what conditions to scale up and down. This includes specifying which Launch Configuration to use when scaling up/down, in fact this is the most important your autoscaling group requires. The autoscaling group resources needs to know which launch configuration to use when starting new instance as part of the scale up process. Other info you need provide are:
- what vpc, along with what subnets to build into. You can specify multiple subnets, so it's best to specify subnets in different AZs to improve high availability, and fault tolerance.<strong> Note the ELB that you attach to this autoscaling group needs to be configured to send traffic to the same list of subnets</strong>. 
- scaling policies, e.g. min, max, and desired ec2 instance numbers. 
- you can specify whether or not the instance managed by your AGS group is to receive traffic from an ELB, if so you specify the name of the ELB. This effectively ends up attaching the ASG to an ELB. Behind the scenes, the AGS will constantly update the ELB's list of EC2 instances. As part of this you can also specify whether to use ELBs or EC2 healthcheck (this is a radio button option). I.e. if you choose ELB healthchecks, then the ASG will create new instance if ELB reports that an ec2 instance has failed.
- You can specify cloudwatch metric ranges for scaling up and down  

ELB - here