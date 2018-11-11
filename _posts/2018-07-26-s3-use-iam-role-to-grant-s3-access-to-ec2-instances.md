---
ID: 3790
post_title: 'S3 &#8211; Use IAM role to grant S3 access to ec2 instances'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/uncategorized/s3-use-iam-role-to-grant-s3-access-to-ec2-instances
published: true
post_date: 2018-07-26 08:52:58
---
Not sure if this is article works (at least it works without setting up any bucket policies). Need to investigate further. 

I recently discovered that you don't need to set up S3 bucket policies in order to give an EC2 access to an s3 bucket (or folder). Say you want to copy files to an s3 bucket from an EC2 instance:

<pre>
$ aws s3 cp /path/to/testfile.txt s3://s3-bucketname/path/to/s3-bucket-folder
</pre>

Then you need to do the following:

1. attach an IAM role to your ec2 instance
2. create the following policy: 

<pre>
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "s3:*"
            ],
            "Resource": [
                "arn:aws:s3:::s3-bucketname",
                "arn:aws:s3:::s3-bucketname/path/to/s3-bucket-folder"
            ]
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": [
                "s3:ListAllMyBuckets",
                "s3:HeadBucket"
            ],
            "Resource": "*"
        }
    ]
}
</pre>

3. attach this policy to your IAM role. 
4. you might need to reboot the ec2 instance for the changes to take effect.