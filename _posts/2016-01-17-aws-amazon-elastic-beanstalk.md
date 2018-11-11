---
ID: 521
post_title: 'AWS &#8211; Amazon Elastic Beanstalk'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-amazon-elastic-beanstalk
published: true
post_date: 2016-01-17 00:00:00
---
Elastic Beanstalk service automates the building of a middleware servers (e.g. httpd servers, nginx servers,...etc) and then deploys your app into it. I.e. it is perfect for setting up a vanilla httpd server, or Rails server,...etc. This is ideal if you have a relatively simple application you want to deploy, that doesn't require a lot of middleware configurations.

Elastic Beanstalk basically does the following:
<ul>
 	<li>deploys   - install middleware and deploys apps into it.</li>
 	<li>manages - e.g. OS patching, configuring firewalld, ..etc.</li>
 	<li>auto scale up/down - add/removes instance to manage demand.</li>
</ul>
&nbsp;

Elastic Beanstalk can install middleware in order to deploy the following types of applications:
<ul>
 	<li>.net</li>
 	<li>java</li>
 	<li>php</li>
 	<li>python</li>
 	<li>ruby</li>
 	<li>docker containers</li>
 	<li>node.js</li>
</ul>
some of the middleware that Elastic Beanstalk can install+configure are:
<ul>
 	<li>httpd server</li>
 	<li>nginx server</li>
 	<li>passenger server</li>
 	<li>apache tomcat</li>
 	<li>Microsoft IIS</li>
</ul>
&nbsp;

Elastic Beanstalk achieves its job by making use of:
<ul>
 	<li>EC2</li>
 	<li>Simple Notication Service SNS</li>
 	<li>S3</li>
 	<li>ELB</li>
 	<li>Auto-Scaling</li>
</ul>
&nbsp;

All you need to tell the  Elastic Beanstalk service is:
<ul>
 	<li>what middleware you want</li>
 	<li>what type of app you have, e.g. ruby app</li>
 	<li>what instance size you want, e.g. t2.micro</li>
</ul>
you can also configure it to deploy applications from  git repos.

Elastic Beanstalk tool comes with it's own <a href="https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb-cli3-install.html?icmpid=docs_elasticbeanstalk_console" rel="nofollow">cli called eb</a>. This CLI can be used to build dev/test/prod environments based on your dev/test/prod git branches. In fact this is the preferred way using beanstalk.

It's easy to deploy software updates, e.g. just do a git pull.

As an alternative to storing the code in git, you can store it in S3, e.g. in the form of a targz file.

&nbsp;

To start using Beanstalk, you first have to create a new "Application":
<p id="JNWZZcV"><a href="http://codingbee.net/wp-content/uploads/2016/05/img_5725cfd573f45.png"><img class="alignnone size-full wp-image-7016 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/05/img_5725cfd573f45.png" alt="" /></a></p>
You just have to specify the type of middleware (ruby. php, etc.) that you want to deploy.

It will then get started by building a 'default' environment. You can choose to build multiple environments (dev/test/prod) inside your beanstalk based application.

There are 2 types of environments you can create inside your beanstalk 'application' resource:
<p id="APIhnVq"><a href="http://codingbee.net/wp-content/uploads/2016/05/img_5725d17aa2a60.png"><img class="alignnone size-full wp-image-7017 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/05/img_5725d17aa2a60.png" alt="" /></a></p>
If you want an environment that directly handles web requests (e.g. http or https), then you pick web server. However if you want an environment that  runs background jobs, then you choose  <span class="emphasis"><em>worker environment</em></span>. Worker environment also links well with SQS, i.e. it polls the queue to processes queue jobs.

You can create multiple 'production' webserver+worker environments and then link them to create a single multi-tiered environment.

When creating a new web server environment, you will be prompted for the following:
<ul>
 	<li>middleware type, e.g. PHP, Tomcat, Docker, ...etc.</li>
 	<li>Environment type, here you have the option to set up environment as a single instance, or autoscaling+loadbalancing</li>
 	<li>You then get the option to upload your code, either by specifying an s3 based url to a zip file, or upload a zip file from your local desktop</li>
 	<li>you get a checkbox, to specify whether you want an RDS instance to be created as part of your environment</li>
 	<li>Instance specific details:
<ul>
 	<li>instance type, e.g. t2.medium</li>
 	<li>EC2 key pair, in order to ssh into your EC2 instance.</li>
 	<li>Application healthckeck url - what url to ping to check for health</li>
</ul>
</li>
 	<li>Which VPC to create the environment in, along with ticking  which subnets to build the ELB and EC2 instances in.</li>
</ul>
&nbsp;

Each web server environment comes with it's own unique url. You can use this to view your Elastic Beanstalk environment's homepage, which is the php apache's home page, if you selected apache.

You can then add a user friendly url name to point to your environment's ELB. The ELB should be listed in the dropdown list when your creating a route 53, alias based entry.

&nbsp;

&nbsp;

https://aws.amazon.com/elasticbeanstalk/ (watch the video)