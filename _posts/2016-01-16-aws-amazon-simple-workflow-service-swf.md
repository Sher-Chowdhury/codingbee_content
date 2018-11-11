---
ID: 518
post_title: 'AWS &#8211; Amazon Simple Workflow Service (SWF)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-amazon-simple-workflow-service-swf
published: true
post_date: 2016-01-16 00:00:00
---
In companies you can document business processes in the form of swim-lane diagrams.

Some lanes requires human interaction (e.g. clicking the "approve" button), whereas other lanes are automated.

For example you went on a business meeting to another city and you want to claim for hotel/travel expenses. Then you submit it online. Then it goes to manager to approve it, then it goes to hr to approve it as well, then an automated system credits your bank account with a refund. All this can span over a couple of weeks.

SWF is designed to automate this as far as possible. SWF is long term processing workflow solution.

SWF has built in auto-scaling up/down (i.e. auto-create/destroy EC2 instance as and when necessary).

SWF can be configured to use onsite servers rather than EC2 instances.

SWF garantees execution of workflow no matter how long it takes, i.e. it doesn't hang. This means it always reaches the end of the swim-lane diagram.

&nbsp;
<h2>Distinctions between SQS and SWF</h2>
In SQS you have "workers" that regularly polling the SQS for new work, when they are idle.

In SWF however, the SWF service delegates "task" to the "workers" which then performs the work and notifies SWF that it has been completed. There are 2 types of tasks that can be delegated to the workers to perform:
<ul>
 	<li><strong>Activity tasks</strong> - The worker performs a routine piece of work and reports back to the SWF service once it is done. SWF, then moves on to the next step of the workflow.</li>
 	<li><strong>Decision tasks</strong> - This type of task contains information but the current state of the workflow. The worker that receives this type of task (is often referred to as a 'decider'), reviews the state of the workflow before deciding on what to do next.</li>
</ul>
A worker/decider can come in many forms, such as an EC2 instance, or even a human (e.g. has to press the approve/reject button).

So in SQS we have "messages" in the queue, whereas in SWF we have "tasks" which are performed in specific order through the workflow.

SWF doesn't have a GUI, instead it is an API the programmers integrates workers/deciders into. This means that workers/deciders can be something outside of aws, e.g. a vm running on microsoft azure.

A workflow execution is allowed to last up a maximum of one year.

.

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

https://aws.amazon.com/swf/