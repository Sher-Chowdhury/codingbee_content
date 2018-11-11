---
ID: 500
post_title: 'AWS &#8211; About this course'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-about-this-course
published: true
post_date: 2016-01-13 00:00:00
---
AWS CSA (associate level) - Amazaon Web Services Certified Solutions Architect

You can book this exam at:

http://aws.amazon.com/certification

After getting the AWS CSA (associate level), you can then move onto getting the AWS CSA (professional level) certification

https://www.expeditedssl.com/aws-in-plain-english

https://read.acloud.guru/what-you-need-to-get-aws-certified-5937e613b10f#.ddmu8gmn0

https://cloudacademy.com/quiz/
<h2>Core Architecture Best Practices</h2>
You want to design your product along with the infrastructure they sit on, so that they are:
<ul>
 	<li><strong>Scalable</strong>  - i.e. you app isn't designed to only run on one vm, it can run on cluster  of VMs, but still appear as a single service.</li>
 	<li><strong>Elastic</strong> - i.e. your system recognises when it is under heavy load, and spin up according. Similar it reduces in size when needed.</li>
 	<li><strong>Fault Tolerant</strong> - e.g. if an vm fails then ELB identifies this and stops send jobs to failing vms. Instead redistributes to remaining vms</li>
 	<li><strong>Self Healing</strong> - e.g. if an ec2 instance fails, then AWS identifies this and automatically builds new replacement EC2 instance. Also if files get corrupted then, where possible, an automated system is in place to regenerate that data.</li>
 	<li><strong>Highly Available</strong> - If one AZ goes down, then another AZ in the same region can takes on the load. Also use load-balanced cluster vms.</li>
 	<li><strong>Cost Efficient</strong> - Use as little as possible without signficantly compromising the best practices mentioned above.</li>
</ul>
&nbsp;