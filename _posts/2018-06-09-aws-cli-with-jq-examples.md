---
ID: 3749
post_title: AWS CLI with jq examples
author: sher
post_excerpt: >
  Some sample queries for retrieving
  information from aws cli using jq
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-cli-with-jq-examples
published: true
post_date: 2018-06-09 13:46:39
---
Let's say I have an EC2 instance with the instance id of 'i-08725957c043b6acd' and it has the ec2 tag: env=preprod. To pull this info out using the aws cli and jq, we run:

<pre>
$ aws ec2 describe-tags --output json --filters "Name=resource-id,Values=i-08725987c041b6acd" | jq --raw-output '.Tags[]|select(.Key=="env")|.Value'
preprod
</pre>

Note, you can obtain the instance_id locally like this:

<pre>
$ instance_id=$(curl -s http://169.254.169.254/latest/meta-data/instance-id) 
</pre>