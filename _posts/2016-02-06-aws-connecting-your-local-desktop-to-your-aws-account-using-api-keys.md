---
ID: 536
post_title: 'AWS &#8211; Connecting your local desktop to your AWS account using API Keys'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-connecting-your-local-desktop-to-your-aws-account-using-api-keys
published: true
post_date: 2016-02-06 00:00:00
---
Everything you can do via your aws console can also be done on your local Linux desktop, via the command line. First you need to install the awscli:

<pre>
$ yum install python-pip
$ pip install awscli
</pre>

Next via the web console, via IAM, create a new user, during the creation process you will generate your IAM keys. 

Next on local desktop's command line, run the "aws configure" command:

<pre>
$ aws configure
AWS Access Key ID [None]: AESDRFKSDGJSKDF
AWS Secret Access Key ID [None]: ljlJLJljGjGLafa5454fasdf6sd5sd6sd5f4a
Default region name [None]: <a href="http://docs.aws.amazon.com/general/latest/gr/rande.html#as_region" rel="nofollow">eu-west-1</a>
Default output format [None]:
</pre>
Note: This ends up creating the ~/.aws folder.  the info  

These API keys are unique across the entire AWS worldwide. This means AWS can determine which aws account these API keys is associated to.

After that we could run the following to see if your machine has access to any s3 buckets:

<pre>
$ aws s3 ls
</pre>

Note, this could still fail if the permissions/roles that are have been assigned to the users are insufficient.