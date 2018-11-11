---
ID: 569
post_title: 'AWS &#8211; Bastion hosts'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-bastion-hosts
published: true
post_date: 2016-04-23 00:00:00
---
For security reasons you should not have your aws resources (e.g. ec2 instances) directly accessible via the internet unless it is necessary, i.e. keep your aws resources internal. However you still want to be able to access your VPCs, there's a few ways to achieve this:
<ul>
 	<li>Setup bastion hosts</li>
 	<li>setup vpn - covered later</li>
 	<li>set up AWS Direct Connect - covered later</li>
 	<li>A combination of the above.</li>
</ul>
A "bastion host" is a general concept and isn't something specific to AWS. A bastion host is essentially an EC2 instance that sits inside a public subnet, which in turn resides inside a vpc. You can ssh into all the other ec2 instances inside this vpc, by first ssh'ing into the bastion host (since by design, all ec2 instances inside a vpc can communicate with each other via their internal ip's irrespective of which public/private subnet they belong to).

&nbsp;

A bastion host has the following characteristics:
<ul>
 	<li>it resides inside a public subnet, therefore your vpc needs to contain at least one public subnet in order to have a bastion host.</li>
 	<li>bastion host is configured with extra security at the os level, e.g. it has selinux enabled, it has firewalld running</li>
 	<li>bastion host should only be configured to have the sshd service listening, best practice is to reconfigure away from default port 22, to another obscure port number. All other ports should be closed.</li>
 	<li>Should have it's own AWS security group which denies traffic on all ports apart from the port that ssh is listening on. This security group should also restrict source IP addresses to the range of your on-premise network.</li>
 	<li>It should store any private ssh keys, instead you use ssh forwarding to seamlessly pass through the bastion host.</li>
</ul>
&nbsp;

&nbsp;

https://10mi2.wordpress.com/2015/01/14/using-ssh-through-a-bastion-host-transparently/

https://10mi2.wordpress.com/2015/01/19/using-ssh-bastion-hosts-with-aws-and-dynamically-locating-them-with-ec2-tags/

http://sshmenu.sourceforge.net/articles/transparent-mulithop.html

Also remember, after restarting your mac, and when you open up a new bash terminal, do:

a. $ ssh-add -l
b. $ ssh-add ~/.ssh/private.pem


http://superuser.com/questions/479592/why-would-ssh-fail-to-expand-h-variable-in-ssh-config

https://www.digitalocean.com/community/tutorials/how-to-configure-custom-connection-options-for-your-ssh-client

https://aws.amazon.com/about-aws/whats-new/2016/09/linux-bastion-hosts-on-the-aws-cloud-quickstart-reference-deployment/