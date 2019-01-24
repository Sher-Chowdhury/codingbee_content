---
ID: 3910
post_title: >
  Give EC2 instance access to a specific
  S3 bucket using IAM
author: sher
post_excerpt: ""
layout: post
permalink: >
  http://codingbee.net/tutorials/aws/give-ec2-instance-access-to-a-specific-s3-bucket-using-iam
published: true
post_date: 2019-01-24 17:21:47
---
Here's the policy you need to attach to your instance's IAM:


<pre>
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "s3:*",
            "Resource": [
                "arn:aws:s3:::bucket-name",
                "arn:aws:s3:::bucket-name/*"
            ]
        }
    ]
}
</pre>

You can replace the '*' above with more restrictive policies if necessary. 


This approach means you don't need to set up any kind of access keys.