---
ID: 517
post_title: 'AWS &#8211; Amazon Elastic MapReduce (EMR)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-amazon-elastic-mapreduce-emr
published: true
post_date: 2016-01-16 00:00:00
---
3Companies often wants to analyse huge amount of data obtain statistics/metrics, identify trends, or find meaningful insights. These can then be used to influence business decisions.

All this data crunching will require a lot of computing resources. One way to do this is to use a software like <a href="http://www.tutorialspoint.com/hadoop/" rel="nofollow">Hadoop</a> to manage this for you. Hadoop will distribute the data crunching task evenly across a cluster of servers. However setting up a Hadoop cluster can be difficult, time consuming and expensive. That's because:
<ul>
 	<li>You need to buy the servers to make up your cluster</li>
 	<li>Install your OS on them</li>
 	<li>install, configure, and tune Hadoop in your cluster</li>
</ul>
You need to do all this before you can start doing any data crunching.

However with Amazon Elastic MapReduce (EMR), you get a fully managed hadoop service already set up for you.

With Amazon EMR, all your raw data is stored in Amazon S3, and Amazon EMR starts up a Hadoop cluster of instances to crunch through all the data. The output (aka results) from all the number crunching then gets stored in Amazon S3. EMR can use other AWS based service sources/destinations aside from S3, e.g. DynamoDB or Redshift (datawarehouse).

Amazon EMR creates the hadoop cluster for you (i.e. it creates all the EC2 instance that makes up the cluster), and automatically destroys the cluster as soon as it is no longer required (or you can leave it running for future data crunching job). That basically means you only pay for what you use.

With  Amazon EMR you can decide how quickly you want your output/results, and EMR will spin up/down EC2 in order to meet your timeframe.

You can also have multiple EMR clusters for multiple data crunching jobs using the same data set thats stored in S3 (or other AWS services)

In terms of pricing, you only pay for the instances that make up each of your cluster. Also you have to pay for the S3 costs too.

SInce EMR makes use of ec2 instances (that are running Hadoop) behind the scene, it means you can ssh into these instances.   Aside for this exception, EMR is fully managed service too.

&nbsp;

As mentioned before, a Hadoop cluster is made up of EC2 instances. One of these instances acts the Hadoop master, and the rest acts as Hadoop slaves.
<h2>Hadoop Master/Slaves</h2>
If you have a 100GB text file and your not using hadoop, but instead just using a normal EC2 instance that has 16GB of RAM. Then when it attempts to process this file, it first tries to load the whole text file into memory. This will fail since your EC2 instance only has 16GB of ram. when in fact you need 100GB of ram. In this scenario, your EC2 instance will try to use swapdisks which again would cause more problems and performance issues.

That's why Hadoop uses master/slave archictecture.  The master breaks down the data into smaller chunks and distribute the chunks to the slaves. EMR has builtin logic on what the size of the chunks should be, which can  either be 64MB or 128MB. However EMR lets you set this as well, by setting the "input split size" setting. The master then distributes these chunks to the slaves.

The hadoop slave have 2 types of process that are running, "mappers" and "reducers"

EMR is usually used to process huge amounts of unordered data. This data could be in the form of a 10GB text file. EMR is used to process this high amount of data so that we can then run queries to get meaningful info and trends from this data.

To use EMR, you need to write two types of processes (which are a bit like shell scripts). They are called:
<ul>
 	<li><strong>A mapper</strong>: This loads the chunk of data in the RAM, then starts processing it. This processing   involves scanning  the  data and then categorises it. It then sorts all the categories based on size. This process is run the Hadoop slave nodes. You can have multiple mappers running on a slave. EMR decides on this optimum number. However you can override this setting if you want. Having multiple mappers running on a slave, means the hadoop slave can process multiple 64MB/128MB chunks of data in parrallel.</li>
 	<li><strong>A reducer</strong>  - Each category of data that is created by the mapper process is then passed onto a process called a 'reducer'. This process analyzes the data in the category in greater details in order to derive useful metrics about the data. You can also have multiple reducers running on each slave. However the lion share of the processing is performed by mappers, so you normally have at least twice as many mappers than reducers running on an instance.</li>
</ul>
These 2 processes are the core of the big-data processing concept, hence this server is called Elastic MapReduce.

EMR automatically shutdown slaves that become idle

&nbsp;

There are a few open source apps that are designed to run on Hadoop clusters:
<ul>
 	<li><a href="https://hive.apache.org/" rel="nofollow">Apache Hive</a></li>
 	<li><a href="https://pig.apache.org/" rel="nofollow">Apache Pig</a></li>
</ul>
&nbsp;
<h2>What does EMR offer</h2>
When you create an EMR resource, EMR will spin up EC2 instance using AMIs that already has the hadoop software pre-installed. You can log into these EC2 instances.

EMR can make the following AWS service as the big data sources (our output destinations):
<ul>
 	<li>S3</li>
 	<li>Redshift (Datawarehouse)</li>
 	<li>DynamoDB</li>
 	<li>RDS</li>
 	<li>AWS Glacier</li>
</ul>
You can also feed in a "Bootstrapper" into EMR. This passes in configurations into your EMR as part of it's initialization process. This is a bit like an EC2 user-data script, but at the EMR/Hadoop level. The bootstrap is executed just before the hadoop cluster starts, so you can specify custom hadoop configurations to override any defaults.

EMR has builtin logic to determine chunk sizes, and number of mappers that can run on each slaves, that's so that all chunks are loaded into a slave's RAM as much as possible to get the best possible performance. As mentioned earlier you can override the chunk size settings and number of mappers per slave settings.

&nbsp;
<h2>Creating an new EMR cluster</h2>
Here is what you see when creating a new cluster:

&nbsp;
<p id="grbPjNE"><a href="http://codingbee.net/wp-content/uploads/2016/05/img_5725b9641950e.png"><img class="alignnone size-full wp-image-7007 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/05/img_5725b9641950e.png" alt="" /></a></p>
<p id="GjyBFzM"><a href="http://codingbee.net/wp-content/uploads/2016/05/img_5725b98697416.png"><img class="alignnone size-full wp-image-7008 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/05/img_5725b98697416.png" alt="" /></a></p>
Notice you can specify the size of your cluster, and you must have exactly 1 node acting as the master, and all the rest are slaves.

You can also specify the instance types. <a href="http://docs.aws.amazon.com/ElasticMapReduce/latest/DeveloperGuide/TaskConfiguration_H1.0.3.html" rel="nofollow">Note the bigger the instance the more mappers/reducers it can have available per slave</a>  (although you can override these), but also more pricey.

&nbsp;

In a well optimized EMR setup, you should have the maximum amount of mappers that uses up all the available ram without resorting to swaps or EBS. The master's job queue has to be as short as possible. Also you shouldn't have any idle mappers.

One option could be to work out exactly how many chunks there will be, then from this set up an EMR instance to have the same number of mappers available (maxing out the ram in each slave). That way all the chunks end up being processed in parrallel, and you have a zero job queue. This ends up doing the processing in the fastest possible time.
<h2>Use S3 as a filesystem instead of HDFS</h2>
HDFS: HaDoop FileSystem

By default EMR uses it's local filesystems, called HDFS for storing reducer outputs (however the S3 filesystem is also installed by default too, but it's muli-part upload feature isnt enabled, but can be enabled via bootstrap). There are 2 problems with this:
<ul>
 	<li>The outputs isn't persistant, i.e. the outputs gets deleted when you delete the EMR cluster. The way round this is to copy the output to S3.</li>
 	<li>Copying outputs to S3 is an unnecessary extra step that takes up time. A better option is mounting S3 as a filesystem, as discussed next</li>
</ul>
If your data source is in S3, then you can kill several birds with one stone by mounting an S3 bucket as a filesystem on your slaves. The benefits of this is:
<ul>
 	<li>eliminates the extra step of copying data from S3 to Hadoops internal filesystems, hence saves time and money.</li>
 	<li>eliminates the extra step of copying output  Hadoops internal filesystems to S3, hence saves time and money.</li>
 	<li>Avoids losing the outputs that are stored in HDFS</li>
</ul>
Even if the data source is elsewhere, e.g. rds, it's still better to S3 for 2 of three benefits listed above.

The Master only responsibility is to know where the data is coming, break it into chunks and distribute the chunks to all available mappers on slaves.

&nbsp;

https://aws.amazon.com/elasticmapreduce/ (watch the video)