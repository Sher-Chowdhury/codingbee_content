---
ID: 532
post_title: 'AWS &#8211; Creating new users (in IAM)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-creating-new-users-in-iam
published: true
post_date: 2016-02-06 00:00:00
---
Once you have logged into the dashboard, you can create new AWS login accounts by clicking on the "Identity and Access Management" link.

When creating a new user, you will get prompted on whether you want to have an "API access key", if you did then you will get the following pair info generated:
<ul>
	<li>Access Key  ID - a string of characters</li>
	<li>Secret Access Key - a really long string of characters</li>
</ul>
The  "Secret Access Key" is  only displayed the one time, and are viewable on an other setting page. Hence it gives you the option to download them into a text file, while they are displayed on screen. If you lose this info, then you would need to regenerate your API keys again, which you can do without needing to recreate the user account.

&nbsp;

Once you have created the user, you will see the user has a unique id called "user ARN". This unique looks something like this:

&nbsp;
<pre>arn:aws:iam::654065406154:user/admin</pre>
<pre>arn: amazon resource number</pre>
The number string, is actually your aws account's unique ID. This number is important because the user needs this number in order to log into the aws account, that's because this number makes up part of the aws web login console. That is, after you have created a new user account, in order for that user to log into aws, they would need to use the following url:
<pre>https://654065406154.signin.aws.amazon.com/console


</pre>
&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;