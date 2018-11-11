---
ID: 515
post_title: 'AWS &#8211; Amazon DynamoDB'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-amazon-dynamodb
published: true
post_date: 2016-01-16 00:00:00
---
"DynamoDB" is a "NoSQL" database rather than a relational db.

A relational db stores data about an object across a number of tables to avoid duplicate data. However in NoSQL, each object is stored in it's own document. This means that  NoSQL performs faster compared to relational dbs since it doesn't need to construct a "joined table". sql db's performance gets worse the more data it houses whereas with NoSQL it can scale more easily. However there are lots of data duplication in NoSQL therefore a   NoSQL db requires more disk space than sql db to store the same information.

Note: <a href="https://en.wikipedia.org/wiki/MongoDB">MongoDB </a>is an example of a NoSQL database.

&nbsp;

"Amazone DynamoDB" is a NoSQL  based fully-managed db managed service. As mentioned earlier, fully-managed covers:
<ul>
	<li>auto-scaling up and down</li>
	<li>underlying OS maintenance done by AWS</li>
	<li>AWS does the fault-tolerance and high-availability for you.</li>
</ul>
&nbsp;

&nbsp;

https://aws.amazon.com/dynamodb/getting-started/