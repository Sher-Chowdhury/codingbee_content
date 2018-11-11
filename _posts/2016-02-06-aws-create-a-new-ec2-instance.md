---
ID: 534
post_title: 'AWS &#8211; Create a new EC2 instance'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-create-a-new-ec2-instance
published: true
post_date: 2016-02-06 00:00:00
---
to create a new EC2 instance, you need to  login into your dashboard, and click on  "EC2", here you'll find that you are already in a default VPC. Next you:

&nbsp;

<em>click on "Launch instance" to create a new instance -&gt; Select OS type (windows, rhel, suse...etc) -&gt;   select vm size (e.g. t2.micro).  </em>

&nbsp;

On the next screen, you can select the role you want to allocate to your ec2 instance. Remember this can't be removed/changed in anyway after the machine has been created.

On this screen you should also set "Auto-assign Public IP" to "enable" from the dropdown list. This is so that you can ssh into your ec2 instance  using putty.

&nbsp;

The next screen let's you attach additional EBS devices to your isntance. These ebs devices appears as /dev/xvda,  /dev/xvdb,  /dev/xvdc,  /dev/xvdd....etc on your machine.

&nbsp;

The next screen lets you tag (i.e. label) your EC2 instance, you can assign multiple tags. Each tag is a key value pair, e.g. server-type=Webserver. Sidenote: you can think of these as a becoming part of your server's environment variables, which in turn can be used in the form of "puppet facts"   and could come in handy for ansible/puppet runs.

The next screen asks for what ports the instance is allowed to listen on. port 22 (ssh) is already added by default. But you open up other ports, e.g. http (80) and https (443).

The next screen is the review screen. Here you have the "Launch" button.

When you click on the launch button, a pop up appears about private/public rsa keys. Here you can choose to create (or choose an exsiting) private/public key. What's actually is happening here is that you get to download the private counterpart of the pair, and the public part will get copied into your EC2 instance, under a "nominated user account".

You need to read your AMI image's documentation to find out what is your nominated user account's username is, that has been baked into your AMI image.

The private key that you download will have a filename with the suffix *.pem. However you can think of it as a id_rsa file.

Let's assume for all the remaining article that the "nominated user account" username is "ec2-user".  Also let's assume that we named the .pem file as id_rsa.pem.

&nbsp;

from your local linux desktop machine, you can then ssh into your EC2 instance by running the command:

&nbsp;
<pre>ssh -i /path/to/id_rsa.pem ec2-user@{ip number}</pre>
&nbsp;

Here we are explicitly specifying which private key file to use by using the identity_file option on the command line.

To make it easier, we could just create this user on our local desktop:

&nbsp;
<pre>$ groupadd ec2-user
$ su -  ec2-user</pre>
</pre>

And then log drop then do:


<pre>
$ mkdir /home/ec2-user/.ssh
$ cp /path/to/id_rsa.pem /home/ec2-user/.ssh/id_rsa
$ chmod 400 /home/ec2-user/.ssh/id_rsa
</pre>

After doing all that, you should now be able to just do:

<pre>[ec2-user@LinuxLaptop ~ ] $ ssh {ipnumber}</pre>

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;