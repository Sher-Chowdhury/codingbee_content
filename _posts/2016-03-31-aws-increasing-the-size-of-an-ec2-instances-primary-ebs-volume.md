---
ID: 554
post_title: 'AWS &#8211; Increasing the size of an EC2 instance&#8217;s primary EBS volume'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/uncategorized/aws-increasing-the-size-of-an-ec2-instances-primary-ebs-volume
published: true
post_date: 2016-03-31 00:00:00
---
This is really good guide:



http://cloud.tekgoblin.com/2013/04/29/aws-guides-how-to-increase-your-ec2-linux-root-volume-size/


basically stop your instance, detach the primary volume (/dev/sda1), create a snapshot from this ebs volume, create a volume (with increased diskspace) from the snapshot, attach the new bigger volume to the ec2 instance, as /dev/sda1. 

start the instance again.