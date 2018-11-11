---
ID: 505
post_title: 'AWS &#8211; Elastic Load Balancer (ELB) overview'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-elastic-load-balancer-elb-overview
published: true
post_date: 2016-01-14 00:00:00
---
ELB is used for distributing traffic to a group of servers. Hence a group of servers shares the same load so that each server doesn't get overwhelmed.

It commonly uses the round robin approach to distribute work amongst the servers. However this approach can be adjusted by enabling "sticky sessions". You can add EC2 instances to an elb. ELB recieves traffic via <strong>route 53</strong> domain aliases. ELB can actually distribute traffic across EC2 instances that are in different availability zones, which maximizes redundancies. Hence if an instance (or AZ) goes down for whatever reason, then the ELB simply stop sending requests to that instance (or AZ).

ELB combined with autoscaling gives the following benefits:
<ul>
	<li>fault tolerance (i.e. prevents an instance from burn out)</li>
	<li>scalability</li>
	<li>elasticity</li>
	<li>high-availability (if one AZ goes down then remaining AZs jumps in to help out.)</li>
	<li>Automatically stop sending traffics to instances that it has identified that have become unhealthy</li>
	<li>Can configure ssl certificates on the ELB rather than each EC2 instances.</li>
</ul>
After you create an ELB, you will find that your elb has it's own dedicated url, e.g.:
<p id="sxyJORJ"><img class="alignnone size-full wp-image-6741 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf719574953.png" alt="" /></p>
&nbsp;

&nbsp;
<h2>Public Vs Internal ELBs</h2>
Internal ELBs are ELBs which can't accept traffic from the public, instead it receives traffic from other resources inside AWS and passes them on.

Private EC2 instance: instance that don't have a public ip address, and consequently don't belong to a public instance. Instead they only have a private ip address and can only recieve traffic from other internal sources, that are also inside the same vpc.

An EC2 instance can recieve public traffic in one of 2 ways:

- by Having a public ip address, and consequently be part of a public subnet. This approach isn't a good idea, sometimes you don't want your EC2 to be directly exposed to the public, i.e. you want EC2 instances to be private EC2 instances.
- Create a public ELB, which receives traffic from outside, and then redistributes to one or more private EC2 instance. A public ELB has a internet gateway attached to it.

Now if you have private EC2 instance that you want to send public traffic to, then you need to create a public ELB. This public ELB need 3 things:

- One or more private EC2 instances. Let's assume we only have one for the time being, which resides in AZ "London"
- A private subnet which the EC2 instance is attached to (this is done by createing a VPC).
- A public subnet, that must also be in the same AZ as the private EC2 instance (this is also done by creating )

For the ELB to work, it needs to be

&nbsp;
<h2>Create an ELB</h2>
This is done by going in the EC2 section of the aws web console.
<p id="YOxDeHl"><img class="alignnone size-full wp-image-6734 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf6b087f01f.png" alt="" /></p>
&nbsp;
<p id="abFgDNc"><img class="alignnone size-full wp-image-6735 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf6b40abbd4.png" alt="" /></p>
&nbsp;

An ELB can only be created inside a vpc. So if you select a vpc from the dropdown list.

As part of the elb process, you have to select one subnet, choose public-subnet.

Also as part creating the elb, you have to specify which ec2 instance the elb should be attached to, i.e. send traffic to.

After you have created the elb, your elb has a url to access it with:
<p id="GwYlGxO"><img class="alignnone size-full wp-image-6743 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf72a6c539c.png" alt="" /></p>
Which is connected to our instance:

&nbsp;
<p id="uUqoscY"><img class="alignnone size-full wp-image-6744 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf72f010520.png" alt="" /></p>
&nbsp;

At this point, you should be able to access your ec2 via it's ip address:

&nbsp;
<p id="NznWcGy"><img class="alignnone size-full wp-image-6745 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf7365f2250.png" alt="" /></p>
Or the elb's url:

&nbsp;
<p id="ZEGngZV"><img class="alignnone size-full wp-image-6746 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf73b399395.png" alt="" /></p>
&nbsp;

&nbsp;

Both of this is working because our ec2 instance is attached to the public via the elb, and via the subnet, private-subnet, which is still attached to a route table that is connected to an internet gateway.

We now want to close off direct access to our ec2 instance (i.e. ip based access), so that it can only be accessible via the ELB's url. To do this we need to first create a new route table that isn't attache to an internet gateway:
<p id="DpvfiTL"><img class="alignnone size-full wp-image-6747 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf75e372e2c.png" alt="" /></p>
Now let's confirm that our route table isn't attached to an internet gateway:

&nbsp;
<p id="rsDkcAK"><img class="alignnone size-full wp-image-6748 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf761ff0d5d.png" alt="" /></p>
&nbsp;

Note, the public route table looks like this:

&nbsp;
<p id="ugZkWIt"><img class="alignnone size-full wp-image-6749 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf7647008d4.png" alt="" /></p>
&nbsp;

Now let's switch our private-subnet to use this new private route-table:

&nbsp;
<p id="wDyBBPo"><img class="alignnone size-full wp-image-6750 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf768f6fadc.png" alt="" /></p>
So now we have:

&nbsp;
<p id="gEdhBhn"><img class="alignnone size-full wp-image-6751 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf76b47ba6a.png" alt="" /></p>
&nbsp;

Now when we try to access via ip address we get:

&nbsp;
<p id="iEwkeWy"><img class="alignnone size-full wp-image-6752 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf776b70013.png" alt="" /></p>
&nbsp;

&nbsp;

&nbsp;

But it still works via the elb's url:

&nbsp;
<p id="UXdoSOB"><img class="alignnone size-full wp-image-6753 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf77c480f88.png" alt="" /></p>
&nbsp;

&nbsp;

&nbsp;
<h2>ELB setup process</h2>
There are 4 tabs to fill in when setting up a new elb.

Define Load balancer:
<a href='http://codingbee.net/wp-content/uploads/2016/04/img_57193ae59e4fc.png'><img alt='' class='alignnone size-full wp-image-6946 pastedimages' src='http://codingbee.net/wp-content/uploads/2016/04/img_57193ae59e4fc.png' /></a>



Assign security groups:
<a href='http://codingbee.net/wp-content/uploads/2016/04/img_57193b331beb3.png'><img alt='' class='alignnone size-full wp-image-6947 pastedimages' src='http://codingbee.net/wp-content/uploads/2016/04/img_57193b331beb3.png' /></a>

Configure security settings:
<a href='http://codingbee.net/wp-content/uploads/2016/04/img_57193bb6e1e19.png'><img alt='' class='alignnone size-full wp-image-6949 pastedimages' src='http://codingbee.net/wp-content/uploads/2016/04/img_57193bb6e1e19.png' /></a>

The above is mandatory to fill in if you have specified that the elb needs to listen on port 443. If you have opted to listen only on port 80, then this section just shows:

<a href='http://codingbee.net/wp-content/uploads/2016/04/img_57193c462ca20.png'><img alt='' class='alignnone size-full wp-image-6950 pastedimages' src='http://codingbee.net/wp-content/uploads/2016/04/img_57193c462ca20.png' /></a>


Assign security groups:
<a href='http://codingbee.net/wp-content/uploads/2016/04/img_57193e79ae989.png'><img alt='' class='alignnone size-full wp-image-6954 pastedimages' src='http://codingbee.net/wp-content/uploads/2016/04/img_57193e79ae989.png' /></a>

You need to also configure this on your ec2 instances too, so to open up communication between the elb and it's ec2 instances. Note you can think of an elb as an ec2 instance. in which case the ports it is listening too is anologous to those ports listed via 'ss -atn' command. Where as security groups are the wrapper around the ec2 instance. Your security group needs to have the same ports open as the ports that it is listening too. Your ec2 instance's security group needs to allow traffic that are coming from elb's security group.   


Configure health check:
<a href='http://codingbee.net/wp-content/uploads/2016/04/img_57193c7965800.png'><img alt='' class='alignnone size-full wp-image-6951 pastedimages' src='http://codingbee.net/wp-content/uploads/2016/04/img_57193c7965800.png' /></a>


Your ELB will constantly ping it's ec2 instances. Be careful here, if you are too strict then a newly added ec2 instance might have to wait longer before elb starts sending traffic to it. 




Add ec2 instance:
<a href='http://codingbee.net/wp-content/uploads/2016/04/img_57193d25c3d8c.png'><img alt='' class='alignnone size-full wp-image-6952 pastedimages' src='http://codingbee.net/wp-content/uploads/2016/04/img_57193d25c3d8c.png' /></a>


One thing to note here is that in the autoscaling section, you can attach an ELB to an autoscaling group. In this situation you wouldn't need to add any instances here since the autoscaling group will automatically add them to the ELB for you.

then finally you have tagging:

<a href='http://codingbee.net/wp-content/uploads/2016/04/img_57193d5617164.png'><img alt='' class='alignnone size-full wp-image-6953 pastedimages' src='http://codingbee.net/wp-content/uploads/2016/04/img_57193d5617164.png' /></a>

Note, the autoscaling feature will automatically add/remove instance from elb for you. Also it's best practive to enable cross zone load balancing. This will improve high availability and fault tolerence. 




And then you review everything:

<a href='http://codingbee.net/wp-content/uploads/2016/04/img_5719417ab9eae.png'><img alt='' class='alignnone size-full wp-image-6955 pastedimages' src='http://codingbee.net/wp-content/uploads/2016/04/img_5719417ab9eae.png' /></a>

Once the elb is created, you should then find it has a dns url assigned to it. This url is referred to as a "CName". 

This url is usually complicated looking, however you can give it a friendly name in route 53. you do this by setting up 2 route53 (alias) record sets, one for www, and the other without. 

Note, by design it is impossible to do 'www.{cname}' although is is possible to do 'http://{cname}'. That's why it's better to setup alias record types, as opposed to cname record types for elb urls. 

Note, wait a few minutes for route53 changes to take effect. 




 


<h2>Important info</h2>
- Your ELB should send traffic to EC2 instance that are in different AZs. This will improve fault tolerance and high availability. 
- EC2 instance that are connected to an ELB doesn't need to have public/elastic ip addresses. since it will receive traffic via the private ip address, which the elb will use to send traffic to it. However the ec2 still needs to be in a public subnet. 
- when creating an ELB, you will find a checkbox setting for whether you want your ELB to be an <a href="https://docs.aws.amazon.com/ElasticLoadBalancing/latest/DeveloperGuide/elb-internal-load-balancers.html?icmpid=docs_elb_console">internal ELB</a>. This is useful if you want an ELB that isn't accessible from the internet, and it used to distribute internal traffic only. 
- when creating a new ELB, you have to specify what port numbers to receive traffic from. You can specify one or more port numbers. However you can't specify source IP. 
- If you specify port 443 i.e. https, then new forms opens up prompting you to provide ssl info. It is best practice to <a href="http://docs.aws.amazon.com/ElasticLoadBalancing/latest/DeveloperGuide/elb-create-https-ssl-load-balancer.html">set up ssl on the elb</a> rather than on the ec2 instances themselves.
- elb is a fully managed service, however behind the scenes elbs are essentially ec2 instances that has load balancing software installed on it. These behind the scene instance are not big instances, but they autoscale depending on incoming traffic load. However if there is a sharp spike in traffic then the elb might lag a little while the behind the scenes instances are converted to bigger instance types to handle the higher traffic. If you are expecting a spike like this, then you can contact aws and let them know so that they can increase the size of the elb's internal ec2 instance ahead of time. This concept of increasing elb's internal ec2s ahead of time is called 'pre-warming' 
- The ec2 instance apache httpd's access logs will show all traffic source as the elb's ip address, since requests passes through elb, and therefores mask the original source IP. However to help mitigate this problem, you can <a href="http://docs.aws.amazon.com/ElasticLoadBalancing/latest/DeveloperGuide/enable-access-logs.html">enable access logs</a> for your ELB. These logs are then stored in a s3bucket.
- ELBs setup stateful connectons with ec2 instantces. which allows incoming and outgoing traffic even though the ec2 instance may not have a public/elastic ip address. However you can't do commands like yum install ... if EC2 instance doens't have public/elastic ip address, or ec2 instance. That's because the outbound routing will work, but the outbound packet will only have the private ip address attached to it, so return packet won't even reach the vpc, since it doesn't have public ip address on the envelope.      


&nbsp;