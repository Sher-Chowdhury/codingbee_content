---
ID: 509
post_title: 'AWS &#8211; Amazon S3'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-amazon-s3
published: true
post_date: 2016-01-16 00:00:00
---
<ul>
 	<li>it is object based storage (key-value pairs)</li>
 	<li>Read-After-Write consistency for PUTS of new objects. This means you can access the file straight away after uploading the file to S3 for the first time. However it has "eventual consistency" for files that you are updating/deleting. That's because there is a small time lag while AZs are being synced up.</li>
</ul>
&nbsp;

Each object stored in S3 compromises of the following subcomponents:
<ul>
 	<li>Key (name)</li>
 	<li>Value (data)</li>
 	<li>Version ID (used for tracking object versions)</li>
 	<li>metadata (e.g. tags)</li>
 	<li>ACLs</li>
</ul>
&nbsp;
<h2>Bucket Versioning</h2>
This is a boolean setting that you can enable for a given bucket. After it is enabled, you can't disable it.

S3 does versioning based on a snapshot style. I.e. it doesn't do incremental version. This means if you have a 1GB file, and have 10 version of it, then you will end up using 10GB worth of storage space, hence versioning becomes expensive for big files.

&nbsp;

&nbsp;
<h2>Cross Region replication</h2>
By default each object is replicated across all AZs inside a region, in order to achieve 11 9s durability (with the exception of RRS). You can increase durability further by enabling Cross Region Replication boolean on a bucket. Note this requires versioning to be enabled on the bucket.

You need to create a new bucket in the new region, so that the 2 buckets can keep in sync with eachother. A bit like how rsync works.

Note: existing objects in the bucket won't get replicated, just the newly created objects only.

Replication also requires an IAM role, this will be added for you. You just have to acknowledge it.

&nbsp;

&nbsp;

&nbsp;

&nbsp;

Amazon's Simple Storage Service (aka S3), can store unlimited "objects".

S3 Objects: These are essentially static files with metadata attached to it.

The metadata is in the  form of key-value pairs. Some of these key-value pairs are assigned by aws, whereas others can be user-defined. An example of aws metadata is whether storage type is (reduced reduncany storage) RRS or glacier.

S3  works a bit like how your dropbox folder works, but S3 also has the following features:
<ul>
 	<li>send content via CDN to Cloudfront</li>
 	<li>control who can access to specific objects</li>
 	<li>version control objects (a bit like github)</li>
 	<li>can act as a simple static website, when linked up with route 53.</li>
</ul>
Here are the main characteristics of S3:
<ul>
 	<li>High Availability - Objects stored in s3 are actually synchronized across all AZs in the given region. However it's not synced across regions. However for the biggest region, <strong>US-east-1 (aka US Standard Region)</strong>, the syncing takes a few second longer because all the AZs are so far apart, geographically. So all the AZs will eventually become consistent, after a few seconds, this phenomenon is called "<strong>eventual consistency</strong>". This means when you upload a file, it could get uploaded to one AZ but if an app immediately tries to access it, it might get an error because it is trying to download it from another AZ that is lagging a few seconds behind. All the other regions don't have this problem.</li>
 	<li>S3 provides a 99.999999999% guaranteed uptime. This also means none of your files will get lost or corrupted.</li>
 	<li>S3 provides 99.99% availability. This means that S3 rarely ever goes down for maintenance purposes.</li>
 	<li>S3 is mainly used for file storage</li>
 	<li>In S3, instead of storing files in folders, you store them in things called "<strong>buckets</strong>". bucket names are unique across all regions around the world, i.e. in the same way that a website address is unique.</li>
 	<li>S3 provides unlimited storage, however the more storage space you use, the more you pay.</li>
 	<li>The size of the file can range from 1Byte to up to 5TB.</li>
</ul>
&nbsp;
<h2><strong>Buckets</strong></h2>
In buckets:
<ul>
 	<li>you can set up "<strong>sub namespaces</strong>", these basically like folders that you can use to organise your objects into. In fact inside AWS web consoles they are referred to as folders, e.g. there is a "create folder" button</li>
 	<li>You can only create a maximum of 100 buckets per aws account.</li>
 	<li>Bucket ownership cannot be changed after the bucket has been created.</li>
 	<li>At the time of creating a bucket, you can choose which region   to create it in, across the world. That main reason for this is because the content on S3 buckets may need to be requested by certain parts of the world, therefore to minimize latency, you need to be able to create the bucket that is geographically closer to where it is needed.</li>
 	<li>EC2 instances can transfer data to/form S3 buckets for free, as long as both the EC2 instance and S3 bucket are in the same region. If the S3 bucket is in a different region then it will cost money.</li>
 	<li>Each bucket is owned by whoever created the bucket.</li>
 	<li>each bucket has it's own url (aka endpoint) so to be accessible via a web browser. The structure of this url is "{bucket-name}.s3-website-{region-name}.amazonaws.com". This useful if you want to use your bucket as a static website hoster</li>
 	<li>You can activate logging on a bucket to track all crud related activities.</li>
 	<li>Buckets like all resources can be "tagged" (i.e. labelled) with something meaningful e.g. the name of the app. You can tag your other resources with the same name. In aws this will end up creating "resource groups", which you can then manage as a collective. This is useful because you can have several applications running inside a single aws account.</li>
 	<li>Each object in a bucket has it's own unique download url, which you can use to download, provided you have the necessary permissions.</li>
</ul>
&nbsp;
<h2>Durability and Availability</h2>
S3 comes in three forms, each of which offers a different levels of durability+availability. This in turn is reflected by the pricing:
<ul>
 	<li>Standard S3 - This is the most expensive form - and provides 99.999999999% durability, and 99.99% availability</li>
 	<li>Reduced Redundancy Storage - 99.99% durability and 99.99% availability. The pricing of this is in the middle of standard S3 and glacier.</li>
 	<li>Glacier - This is the cheapest form. objects stored here can take 3-5 hours to retrieve. Glacier works really well in conjunction to S3 Lifecycle policies, which is covered further down.</li>
</ul>
&nbsp;
<h2>Amazon  S3 pricing structure</h2>
The costs is calculated based on a combination of:
<ul>
 	<li>charges per GB</li>
 	<li>Number of requests</li>
 	<li>data transfers in/out of region. This means that instances can write/read from it's own S3 for free since they are within the same region.</li>
 	<li>works out cheaper if you bulk buy</li>
 	<li>Whether you opt for <strong>RRS (Reduced Redundancy Storage)</strong>. This is cheaper version of S3 which is disabled by default but you can enable it.  The main difference is that it  only offers 99.99% durability compared to standards S3's 99.999999999% durability. If you decide to use RRS, then Amazon will send notification if and when an object is lost/corrupted. In reality this could mean that your objects are synchronised across some AZs rather than all of them in the region</li>
 	<li>If we have enabled object  versioning,  then we have to pay for each versioned object.</li>
 	<li>In a bucket's settings section, you have the option to enable payment requests when another aws user attempts to access the bucket's content. Note, this feature is not compatible with anonymous access.</li>
</ul>
<h2>Amazon S3 security</h2>
Here are some of S3 security features:
<ul>
 	<li>Objects stored in S3 can be encrypted using the "S3 encryption" option.  data transfers in/out of S3 can be done via HTTPS protocol</li>
 	<li>All buckets are private by default - and aws users can be granted access by adding access permissions via IAM, they can then access the content by first logging into aws</li>
 	<li>Publicly share bucket content - You can set up downloadable urls</li>
 	<li>share content temporarily using specially generated unique urls that expires after a certain period of time, e.g. 24 hours.</li>
 	<li>You can share buckets between 2 different aws accounts using Access Control Lists (ACLs)</li>
 	<li>You can apply permissions directly to the bucket itself, e.g.
<ul>
 	<li>e..g set up anonymous access, so that the bucket's content is accessible to the whole public</li>
 	<li>Allow access from certain source IP ranges</li>
 	<li>Allow access based on the http requester. E.g. hotlinking of images to display on a website</li>
</ul>
</li>
</ul>
&nbsp;
<h2>lifecycle policies and object versioning</h2>
In s3 you can do unlimited object versioning. Versioning is something that is disabled by  default and so if you want to use it then you need to  enable it on your s3 bucket. Versioning also keeps track of deleted object, I.e. you can restore deleted objects. Also to save money you can delete particular versions.

Lifecycle policy can be applied at the bucket level, or folder level.

In the same way you enable versioning on an s3 bucket, you can also enable "lifecycle policy" feature, which is used to eventually delete unwanted objects. Here are some common examples:
<ul>
 	<li>So that all versions of an object that's older than 3 months, must be deleted</li>
 	<li>Delete the object (along with all it's versions) if it hasn't been accessed in the last 3 months</li>
 	<li>Archive all versions of an object that are older than 3 months, by sending it to Amazon Glacier</li>
 	<li>Archive by sending  to Amazon Glacier,  all  objects (along with all it's versions) if it hasn't been accessed in the last 3 months</li>
 	<li>You can selectively choose which objects should be actively lifecycled, based on them having a certain prefix in it's filename, or placing them in in sub-namespaces that you have selected to be lifecycled.</li>
</ul>
Lifecycle policy is used to automate the regular housecleaning, which in turn helps reduce the cost of using S3.

Note: once you enable versioning on a bucket, you can't disable it again. The only way around this, is to create a new bucket, migrate content from the old bucket, and then delete the old bucket.
<h2>How can S3 be integrated into application</h2>
S3 isn't just a storage solution. It can act as a place where application can store/retrieve/manage files, similar to how applications store/retrieve/manage data in a database.

here are some of the main uses:
<ul>
 	<li>S3 can be used for hosting static websites, with the help of Route 53</li>
 	<li>S3 can act as the source content for the Cloudfront CDN (Content Delivery Networks). Cloudfront can then cache this data around the world in <strong>edge locations</strong>.</li>
 	<li>Allow people to upload objects to S3 indirectly when using an app, e.g. Dropbox.</li>
 	<li>Make use of the <strong>Multipart</strong> feature, this is a feature you can build into an S3 connected app that allows users to upload files by first splitting the file into smaller parts, and then upload them. This can:
<ul>
 	<li>speed up uploads, if the parts are being uploaded in simultaneously (bandwidth permitting)</li>
 	<li>stop and resume upload</li>
 	<li>Multipart is actually required for uploading really big files, i.e. 5GB or bigger. However Amazon recommends to use Multipart of uploading files of 100MB and bigger.</li>
 	<li>Multipart is feature that you utilize via the the sdk and aws cli</li>
</ul>
</li>
</ul>
&nbsp;
<h2>Event notifications</h2>
You can set up automatic notifications to issue when certain events occur in an S3 bucket (via each bucket's settings section). A common example is when a file gets lost/corrupted while it is in Reduced Reduncancy Storage, RRS. AWS by defualt automatically recognizes these situations and issue a notification. This notification can be sent to number of places including:
<ul>
 	<li>SNS - which could then issue out emails, or send the notification to a website that could regenerate the file, if possible</li>
 	<li>SQS Queue - which could then be use processed in order to regenerate the lost file, if that is possible</li>
 	<li>Lamda - not sure what this is.</li>
</ul>
Another notification that S3 can issue is when an object is created using the API, i.e. when creating via web console, CLI, or SDK. The particular API calls that can create an object (and hence trigger a notification), are:
<ul>
 	<li>copy</li>
 	<li>put</li>
 	<li>post</li>
 	<li>CompleteMultiPartUpload</li>
</ul>
&nbsp;

&nbsp;