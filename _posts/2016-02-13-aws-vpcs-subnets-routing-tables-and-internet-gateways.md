---
ID: 547
post_title: 'AWS &#8211; VPCs &#038; Subnets, routing tables, and Internet Gateways'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-vpcs-subnets-routing-tables-and-internet-gateways
published: true
post_date: 2016-02-13 00:00:00
---
In order for an instance to have internet access, you will need the following:
<ul>
	<li>An internet gateway attached to your vpc</li>
	<li>The internet gateways is associate by an entry in your route table. Note, your VPC can have multiple route tables.</li>
	<li>This route table must be attached to your subnet, which consequently makes your subnet into a public subnet.</li>
	<li>The subnet's attached network acl, must have the appropriate inbound/outbound firewall rules in place to allow the desired traffic through</li>
	<li>your instance must have a public or Elastic IP address</li>
	<li>ensure your security groups and network ACLs allows the relevant traffic to go through</li>
	<li>setting inside instances are correct, e.g.:
<ul>
	<li>firewalld is configured</li>
	<li>network service is running</li>
	<li>services are running and listening on ports.</li>
</ul>
</li>
</ul>
&nbsp;

&nbsp;

First off, a vpc is a way to group your resources. These resources can be in different AZ. Hence a vpc isn't attached to particular AZ.

Your AWS accounts comes with pre-created vpc, called "default":

<img class="alignnone size-full wp-image-6701 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf33cc15fad.png" alt="" />

However in order to create a new vpc, you need to provide the following info:

<img class="alignnone size-full wp-image-6702 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf33e94c6b3.png" alt="" />

The description in the cidr block is:

<img class="alignnone size-full wp-image-6703 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf3409d8101.png" alt="" />

For my example, let's say I create a vpc with the following details:

&nbsp;
<ul>
	<li>Name tag:   cb1</li>
	<li>CIDR block:   10.0.0.0/16</li>
	<li>Tenancy: default   (the only other option in the dropdown list is "dedicated")</li>
</ul>
In this scenario, according t  <a href="http://www.subnet-calculator.com/cidr.php" rel="nofollow">subnet-calculator.com</a>  the full range of ip address available is:
<p id="UuollNG"><img class="alignnone size-full wp-image-6704 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf37a10b5f4.png" alt="" /></p>
I.e., the subnet mask of 16, locked down the first to parts "10.0".

After we have created the vpc, we can view it's details:

&nbsp;
<p id="yqAsPdh"><img class="alignnone size-full wp-image-6705 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf38b23598b.png" alt="" /></p>
&nbsp;

&nbsp;

&nbsp;

&nbsp;

Now we can attach 1 or more subnets to this VPC. To create a new subnet, you need to provide the following info:
<p id="mEdspWb"><img class="alignnone size-full wp-image-6712 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf3f27ca717.png" alt="" /></p>
<p id="bxfOLdy">Also here's the help info for the CIDR block field:</p>
<p id="enPEANn"><img class="alignnone size-full wp-image-6713 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf4088471ff.png" alt="" /></p>
&nbsp;

Notice here that a subnet is associated to a particular AZ, and must also belong to an existing VPC. So in my case I created a subnet with the following details:
<ul>
	<li>Name tag: public-subnet</li>
	<li>VPC: cb1</li>
	<li>Availability Zone: us-east-1a</li>
	<li>CIDR block: 10.0.0.0/24</li>
</ul>
Here the subnet's CIDR range must fall within the VPC, wider CIDR range. In this instance, the range for the cb1's range is 10.0.0.0- 10.0.255.255. So the subnet's mask of  10.0.0.0/24 falls within this range as shown here:
<p id="eMrsymJ"><img class="alignnone size-full wp-image-6715 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf41bb56ab1.png" alt="" /></p>
&nbsp;

After creating this subnet, we now have:
<p id="xAFlytn"><img class="alignnone size-full wp-image-6716 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/aws-vpcs-subnets-routing-tables-and-internet-gateways-6716.png" alt="" /></p>
Any EC2 instance that is connected to this subnet, will have an internal ip address which ranges between 10.0.0.0-10.0.0255.

&nbsp;

Now let's create another subnet for this vpc, which is also in the same AZ. This time with the following details:
<p id="cLtBMUQ"><img class="alignnone size-full wp-image-6718 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf44d239a9e.png" alt="" /></p>
The ip range for this subnet works out to be:
<p id="OcSLaaB"><img class="alignnone size-full wp-image-6719 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/aws-vpcs-subnets-routing-tables-and-internet-gateways-aws-csa-associate-6719.png" alt="" /></p>
This ip range again falls within the vpc range.

After creating this subnet we now end up with:
<p id="whZWIHx"><img class="alignnone size-full wp-image-6720 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf45a7d8f19.png" alt="" /></p>
&nbsp;

Notice that both subnets inherited the vpc's  "route table".

Note, each newly created route table comes prelisted with a default route, referred to as the <a href="http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Route_Tables.html">local route</a>. This local route has an IP range that covers the entire vpc IP range. This is what makes it possible for each EC2 instance in your VPC to automatically be able to communicate with each other.

In order make  a subnet into public subnet (i.e. be able to receive outside traffic), it needs to be connected to a route table, which in turn is attached to an  "internet gateway":

This gateway is attached to the default VPC's subnet's routing table.

<a href="http://codingbee.net/wp-content/uploads/2016/01/architecture_linux.png" rel="attachment wp-att-6421"><img class="alignnone size-full wp-image-6421" src="http://codingbee.net/wp-content/uploads/2016/01/architecture_linux.png" alt="" width="603" height="436" /></a>

&nbsp;

Source: <a href="http://docs.aws.amazon.com/gettingstarted/latest/wah-linux/web-app-hosting-intro.html">Amazon</a>

Now by default, when you create a new vpc,  a "main" (default) route table also get's created with the following rule:
<p id="qdJPTCi"><a href="http://codingbee.net/wp-content/uploads/2016/02/img_56c4cd3bc021c.png"><img class="alignnone size-full wp-image-6807 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56c4cd3bc021c.png" alt="" /></a></p>
This is an example for a vpc with cidr block of 10.10.0.0/16. This routing table automatically get's assigned to any subnets that you create inside this vpc. This route allows routing of traffic from instance in one subnet to reach an instance in another subnet.

&nbsp;

&nbsp;

&nbsp;

Now let's create an internet gateway, to create a gateway, you just need to provide a name for the internet gateway:
<p id="sDEOLck"><img class="alignnone size-full wp-image-6721 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf49eea5b62.png" alt="" /></p>
&nbsp;

Now we have ended up with:

&nbsp;
<p id="ITTQsbn"><img class="alignnone size-full wp-image-6723 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf4d76ac57e.png" alt="" /></p>
&nbsp;

&nbsp;

Note, we already have an internet gateway, which by default is attached to the default vpc.

Next we need to attach our new gateway to our new vpc:
<p id="HApDHuX"><img class="alignnone size-full wp-image-6724 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf5013b30b2.png" alt="" /></p>
&nbsp;

Now we end up with:

&nbsp;
<p id="jplSQLt"><img class="alignnone size-full wp-image-6725 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf504b58d1c.png" alt="" /></p>
Note: A vpc is can only have up to 1 internet gateway attached to it at any one time.

Now we need to link our Interneet Gateway to our new vpc's route table. Here's how to create a route table:
<p id="ixWlzXC"><img class="alignnone size-full wp-image-6726 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf516acc75f.png" alt="" /></p>
As you can see, we simply have to give the route table a name and specify which vpc it belongs to. However we don't need to do any of this because a route table was automatically created at the time of the vpc creation. I just allocated a name "cb1-route-table" to this route table:
<p id="NOPRzYi"><img class="alignnone size-full wp-image-6727 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf520d1b349.png" alt="" /></p>
This route table was automatically created at the time of the vpc's creation. This route table was also automatically attached to both public-subnet, and private-subnet, at the time of both subnet's creation.

Now we need to add a route to our <a href="http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Route_Tables.html" rel="nofollow">route table</a> that specifies that all data is allowed to go in/out via our new internet gateway:
<p id="vicfpsh"><img class="alignnone size-full wp-image-6729 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf554d12622.png" alt="" /></p>
&nbsp;
<p id="UVASAGQ"><img class="alignnone size-full wp-image-6730 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf55b410cbf.png" alt="" /></p>
So that we end up with:

&nbsp;
<p id="qmgsEEW"><img class="alignnone size-full wp-image-6731 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf566a1e168.png" alt="" /></p>
&nbsp;

However since both of our net subnets (private-subnet and public-subnets) are using the same route table:
<p id="ZpJQETc"><img class="alignnone size-full wp-image-6732 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf56bee19d1.png" alt="" /></p>
It means that both subnets are public subnets, whereas we wanted "private-subnet" to be a private subnet (which can only recieve traffic from internal resources, e.g. from an ELB).

Before we make "private-subnet" private, we'll first create the following:
<ul>
	<li>EC2 instance, ensure that this instance is part of this new vpc</li>
	<li>An ELB</li>
</ul>
&nbsp;

Follow this guide to <a href="http://codingbee.net/tutorials/aws/aws-create-a-new-ec2-instance/" rel="nofollow">create an EC2 instance</a>, also make sure to create the in our new vpc.

When creating the ELB, you need to attach it to our new vpc:
<p id="jjReQmo"><img class="alignnone size-full wp-image-6739 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf6d52359b6.png" alt="" /></p>
Note: I also opened up the http+https firewalls because my ec2 is going to act as a web server. We then sroll further down and only   attach  the public-subnet.

In the next section we select all the what ec2 instances that we want to send data traffic to:

{need to add a screenshot}

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa

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

zzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzz
&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

At this point, you should be able to access your ec2 via it's ip address:
<p id="NznWcGy"><img class="alignnone size-full wp-image-6745 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf7365f2250.png" alt="" /></p>
Or the elb's url:
<p id="ZEGngZV"><img class="alignnone size-full wp-image-6746 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf73b399395.png" alt="" /></p>
Both of this is working because our ec2 instance is attached to the public via the elb, and via the subnet, private-subnet, which is still attached to a route table that is connected to an internet gateway.

We now want to close off direct access to our ec2 instance (i.e. ip based access), so that it can only be accessible via the ELB's url. To do this we need to first create a new route table that isn't attache to an internet gateway:
<p id="DpvfiTL"><img class="alignnone size-full wp-image-6747 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf75e372e2c.png" alt="" /></p>
Now let's confirm that our route table isn't attached to an internet gateway:
<p id="rsDkcAK"><img class="alignnone size-full wp-image-6748 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf761ff0d5d.png" alt="" /></p>
Note, the public route table looks like this:
<p id="ugZkWIt"><img class="alignnone size-full wp-image-6749 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf7647008d4.png" alt="" /></p>
Now let's switch our private-subnet to use this new private route-table:
<p id="wDyBBPo"><img class="alignnone size-full wp-image-6750 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf768f6fadc.png" alt="" /></p>
So now we have:
<p id="gEdhBhn"><img class="alignnone size-full wp-image-6751 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf76b47ba6a.png" alt="" /></p>
Now when we try to access via ip address we get:
<p id="iEwkeWy"><img class="alignnone size-full wp-image-6752 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf776b70013.png" alt="" /></p>
But it still works via the elb's url:
<p id="UXdoSOB"><img class="alignnone size-full wp-image-6753 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/02/img_56bf77c480f88.png" alt="" /></p>
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

http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Subnets.html

https://aws.amazon.com/vpc/

http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Subnets.html