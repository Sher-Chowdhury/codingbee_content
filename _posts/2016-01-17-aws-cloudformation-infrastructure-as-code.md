---
ID: 522
post_title: 'AWS &#8211; CloudFormation (Infrastructure As Code)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-cloudformation-infrastructure-as-code
published: true
post_date: 2016-01-17 00:00:00
---
You can manage all your AWS stuff using the AWS gui web console.  However everything that you can do using the gui console, can also be done by using either the:
<ul>
 	<li>AWS API, which are accessible via a choice of <a href="https://aws.amazon.com/tools/" rel="nofollow">SDKs</a>.</li>
 	<li><a href="https://aws.amazon.com/tools/">AWS CLI</a> (Command Line Interface) - Linux or Powershell</li>
</ul>
CloudFormation on the other hand lets you document your entire AWS infrastructure (i.e. a vpc) in the form of a json file.

In other words,  CloudFormation is the AWS equivalent of writing a vagrantfile for vagrant. The only difference being that your are writing about aws stuff and it is written using json syntax.

You can then store this json file in github and quickly build/rebuild new vpc instances from it. The json file can specify only part of a vpc environment rather than a whole environment, e.g. it could document just a single EC2 instance.

<strong>Stack</strong>: this is an instance of a CF template. You can use the template to created multiple stacks, e.g. a dev stack and a prod stack. A stack is essentially a collection of resources (ec2 instances, rds instances,...etc).

&nbsp;

A CF template can create these resources inside an existing VPC, in which case you need to add a parameter in your CF template for specifying the VPC's ID, or you can write a CF template to create the entire VPC along with all the resources that goes inside it.

<a href="http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-anatomy.html">Anatomy of a template</a>.

&nbsp;

http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/gettingstarted.templatebasics.html

http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/GettingStarted.Walkthrough.html

https://aws.amazon.com/cloudformation/aws-cloudformation-articles-and-tutorials/

http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-using-cloudformer.html

https://aws.amazon.com/cloudformation/aws-cloudformation-templates/

http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/GettingStarted.Walkthrough.html

https://aws.amazon.com/cloudformation/

https://cloudacademy.com/library/?q=cloudformation&amp;label=course

https://www.youtube.com/watch?v=fVMlxJJNmyA

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;