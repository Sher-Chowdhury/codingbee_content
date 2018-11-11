---
ID: 553
post_title: 'AWS &#8211; Setup Dashing on EC2 instance that&#8217;s built from centos 7 ami'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/uncategorized/aws-setup-dashing-on-ec2-instance-thats-built-from-centos-7-ami
published: true
post_date: 2016-03-31 00:00:00
---
I had to install:

<pre>
Yum install epel-release
Yum install curl
Yum install libxml2
Yum install libxml2-devel
yum install libcurl-devel
Yum install rubygems
yum install 'gcc-c++'
Yum install ruby-devel
yum -y install nodejs
</pre>

As normal user: run:

<pre>
$ Gem install bundler
</pre>