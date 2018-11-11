---
ID: 560
post_title: 'AWS &#8211; DB Subnet Groups'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-db-subnet-groups
published: true
post_date: 2016-04-10 00:00:00
---
A subnet by design is attached to a particular AZ. So all instances/resources that reside in a subnet actually exists in the same AZ.

However RDS has features such as Multi-AZ and Read Replicas, which are used for improved redundancies and performance reasons. However these features needs to know what other subnets it is allowed to build read replicas and multi-AZ passive/standby db's into. You can tell this to RDS by providing a list of subnets, this list is referred to as a DB subnet group.

Note: if you want your read replicas and Mult-AZ standbys to be accessible via the internet, the subnets that you add to your DB subnet group needs to be public subnets (i.e. the subnet's route table must have an entry pointing to your vpc's internet gateway).

When you create a new rds instance, you will have the option to specify whether it is publicly accessible (whether on not to attach a public IP address to your instance).

&nbsp;