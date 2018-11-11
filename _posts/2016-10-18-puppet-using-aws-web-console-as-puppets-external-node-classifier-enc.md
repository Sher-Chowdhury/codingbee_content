---
ID: 590
post_title: 'Puppet &#8211; Using AWS web console as Puppet&#8217;s external node classifier (ENC)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/uncategorized/puppet-using-aws-web-console-as-puppets-external-node-classifier-enc
published: true
post_date: 2016-10-18 00:00:00
---
This is a script I wrote that queries the ec2 tags of an aws console, in order to figure out what environment a node belongs to, and what class to assign to it. 


<pre>
#!/bin/bash

# https://docs.puppetlabs.com/guides/external_nodes.html
# http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html

export AWS_ACCESS_KEY_ID=xxxxxxxxxxxxxxxxxxxxxx
export AWS_SECRET_ACCESS_KEY=xxxxxxxxxxxxxxxxxxxxxx
export AWS_DEFAULT_REGION=xxxxxxx

instanceid=`echo $1 | awk -F"_" '{print $NF}'` # $1 will match the certname

env=`/bin/aws --output text ec2 describe-instances --instance-ids $instanceid | grep '^TAGS' | grep 'env' |   awk '{print $NF}'`
role=`/bin/aws --output text ec2 describe-instances --instance-ids $instanceid | grep '^TAGS' | grep 'role' |   awk '{print $NF}'`

#aws ec2 describe-instances --instance-ids $instanceid > /tmp/enc-log.txt
#echo "puppet run occured at `date`" > /tmp/enc-log.txt
#echo "The first param value is: $1" >> /tmp/enc-log.txt
#echo "The environment is: $env" >> /tmp/enc-log.txt
#echo "The puppet role is: $role" >> /tmp/enc-log.txt

echo '---'
echo 'classes:'
echo "   - roles::$role"
echo "environment: $env"   
echo "parameters:"
echo "   role: $role"                 # Needed for hiera lookup
</pre>

In order for this script to work, you need to install the aws cli utility on the puppetmaster, also ensure your puppetmaster's ec2 IAM role has the necessary privileges.