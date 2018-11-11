---
ID: 525
post_title: 'AWS &#8211; Create IAM user accounts'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-create-iam-user-accounts
published: true
post_date: 2016-01-17 00:00:00
---
After creating a new user, the user will have a unique ID called a "User ARN" account. It looks something like this for username called "admin":

arn:aws:iam:870464616830:/user/admin

The string of digits indicates the actual aws account.

&nbsp;

IAM user's can log into their account using the AWS account's custom login url, which based on the above example will look something like this:

https://870464616830.signin.aws.amazon.com/console

&nbsp;