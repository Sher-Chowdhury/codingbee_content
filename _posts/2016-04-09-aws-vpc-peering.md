---
ID: 559
post_title: 'AWS &#8211; VPC Peering'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-vpc-peering
published: true
post_date: 2016-04-09 00:00:00
---
All resources inside a vpc can automatically communicate with each other via their private ip addresses, irrespective of which public/private subnets they belong to. However it is also possible for resources in one vpc to communicate with resources in another vpc. This is done by setting up a "vpc peering" connection.

A VPC peering connection is a network connection between two VPCs which lets instances from one vpc to  communicate with instances in the other vpc  as if they are within the same network.

There are a few conditions that needs to be met for setting up vpc peering:
<ul>
 	<li>Both VPCs needs to reside in the same region. I.e. you can't set up vpc peering between vpc's in different regions.</li>
 	<li>Each VPC's CIDR block range  is not allowed to overlap. Otherwise it would potentially mean that an EC2 instance in one vpc can have the same private ip address as an EC2 instance in the other VPC, which results in conflicts.</li>
 	<li>VPCs can reside in different AWS accounts, however the region restriction still applies.</li>
</ul>
&nbsp;

There are a few options to set how much access resources in one vpc is allowed to have access to resources in another vpc:
<ul>
 	<li>vpc-vpc : all resources in one vpc can access all resources in the other vpc</li>
 	<li>subnet-vpc</li>
 	<li>subnet-subnet</li>
 	<li>instance-vpc</li>
 	<li>isntance-subnet</li>
</ul>
Note: you can't use vpc peering to setup communications between your vpc and on premise network, to do this you need to set up a vpn instead.

A common scenario you can have is have a single "master" vpc, and lots of slave VPCs, where all the slave VPCs are peered connected to the the master VPC. This means that the slave vpc's can't communicate with eachother, although they are connected to the master VPC.