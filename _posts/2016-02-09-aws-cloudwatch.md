---
ID: 543
post_title: 'AWS &#8211; Cloudwatch'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-cloudwatch
published: true
post_date: 2016-02-09 00:00:00
---
<h2>Status checks</h2>
Cloudwatch is a monitoring service.  It can monitor for 2 types of checks:
<ul>
 	<li><strong>System Status Checks</strong></li>
 	<li><strong>Instance Status Checks</strong></li>
</ul>
<h3>System Status Checks</h3>
These are checks that gives information about whether aws underlying hardware/software has developed a fault. If any of these checks fails then it is something AWS is required to repair. Here are some examples checks of this type:
<ul>
 	<li>Loss of network connectivity</li>
 	<li>Loss of system power</li>
 	<li>Software issues on the physical host</li>
 	<li>Hardware issues on the physical host</li>
</ul>
When one of these system checks fails, it can be fixed in one of 2 ways:
<ul>
 	<li>Wait for AWS to fix the issue</li>
 	<li>Stopping and starting an instance, or by terminating and replacing an instance.  Behind the scenes, this has the effect automatically moving your instance to working hardware.</li>
</ul>
<h3>Instance Status Checks</h3>
These are checks that gives information about the software and network configuration of your individual instance. These checks detect problems that requires you  to fix, e.g. fix a faulty config file. Here are some examples of this type of checks:
<ul>
 	<li>Failed system status checks - i.e. this is to show that Instant Status checks will indicate if the underlying hardware/software is at fault.</li>
 	<li>Incorrect networking or startup configuration</li>
 	<li>Exhausted memory</li>
 	<li>Corrupted file system</li>
 	<li>Incompatible kernel</li>
</ul>
<div class="itemizedlist"></div>
Faults that are identified by these types of checks requires you to investigate and fix.

&nbsp;
<h2>Cloudwatch Alarms</h2>
Cloudwatch alarms is service that sends out simple notification service (sns) message when a status check changes state. An alarm can have 3 states:
<ul>
 	<li><span class="emphasis"><em><code class="code">OK -  </code></em></span>The metric is within the defined threshold</li>
 	<li><span class="emphasis"><em><code class="code">ALARM -  </code></em></span>The metric is outside of the defined threshold</li>
 	<li><span class="emphasis"><em><code class="code">INSUFFICIENT_DATA  </code></em><code class="code">-  </code></span>This is temporary that a new alarm takes in the first few minutes after creating it</li>
</ul>
Cloudwatch Alarms are enabled by default for  a few key status checks. These status checks are the ones that can be monitored at the physical host level, e.g.:
<ul>
 	<li>cpu utilisation</li>
 	<li>network traffic</li>
 	<li>disk usage</li>
</ul>
CPU credit usage/balance - i.e. it monitor how much IOPS you are accruing, on an burstable instance.

Cloudwatch alarms for each instance is kept for several weeks after an instance has been deleted.

You can also set alarms if your instance reaches a certain cost to run, e.g.  £50/day. This is useful to notify if you forgot to shutdown an instance after you finished working with it. Another alarm could be set if data transfer costs becomes to high e.g.  £20/day.

Alarms can do a lot more then just sending out sns notification, they can also trigger actions to try fix the issue, e.g. an alarm can trigger the creation of more instance to handle unexpected high load.  Hence Cloudwatch Alarms plays a big part to enabling elasticity/auto-scaling.

&nbsp;
<h2>Basic and Detailed Monitoring</h2>
CloudWatch generates  near-realtime metrics by processing raw data from Amazon EC2. Cloudwatch stores up to  two weeks of data, to help analyse the history. By default, Amazon EC2 metric data is automatically sent to CloudWatch in 5-minute periods (aka basic monitoring). however  you can  enable more frequent monitoring on an Amazon EC2 instance, which sends data to CloudWatch in 1-minute periods (aka detailed monitoring). Pricewise,  Basic Monitoring is free, but there is a charge for Detailed Monitoring.

&nbsp;

&nbsp;
<h2>Monitoring using scripts</h2>
Some EC2 instance metrics can only be collected by <a href="http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/monitoring_ec2.html">running monitoring scripts (provided by aws)</a> inside your ec2 instances. For example:
<ul>
 	<li>Memory utilization ("free -m" command)</li>
 	<li>Swap disk utilization (disk space acting as memory)</li>
 	<li>disk space utilization ("du -h" command)</li>
 	<li>All of these are displayed as time based line graphs on the aws console.</li>
</ul>
&nbsp;

http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/monitoring_ec2.html

&nbsp;

&nbsp;

&nbsp;

&nbsp;

http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-cloudwatch.html

&nbsp;

&nbsp;

&nbsp;

http://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/AlarmThatSendsEmail.html

https://aws.amazon.com/cloudwatch/

http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/monitoring-system-instance-status-check.html