---
ID: 561
post_title: 'AWS &#8211; Virtual Private Network (VPN)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-virtual-private-network-vpn
published: true
post_date: 2016-04-11 00:00:00
---
A VPN is essentially a subnet where it's members are a combination of AWS resources and on premise devices. I.e. a a VPN is a subnet that extends to an on premise site. There are 2 ways to set up VPN:
<ul>
 	<li>Hardware based</li>
 	<li>VPN Tunnelling (OpenVPN)</li>
</ul>
&nbsp;
<h2>Hardware based VPN</h2>
To set up a VPN, you need to have the following components:
<ul>
 	<li><strong>Virtual Private Gateway (VPG)</strong> - This is an AWS resource that you create under the VPC section of your AWS console, and then attach it to your vpc, in the same way that you attach other things, e.g. route table. When creating a VPG, all you have to do is to provide a name for the VPG, and specify which VPC to attach it to:
<p id="TYMANKl"><a href="http://codingbee.net/wp-content/uploads/2016/04/img_570c05f575ec7.png"><img class="alignnone size-full wp-image-6907 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/04/img_570c05f575ec7.png" alt="" /></a></p>
</li>
 	<li><strong>Customer Gateway</strong> - This is essentially a device on your on premise network that has a public IP address attached to it. All on premise network traffic that needs to be sent to the VPC will need to be routed through the Customer Gateway. Therefore in practice the Customer gateway is usually your on-premise router. In AWS you need to create a "Customer Gateway" resource which represents your on premise router.
<p id="KihdHXq"><a href="http://codingbee.net/wp-content/uploads/2016/04/img_570c06f3adfad.png"><img class="alignnone size-full wp-image-6908 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/04/img_570c06f3adfad.png" alt="" /></a></p>
Here you choose a name of the customer gateway and specify your router's public IP address. Not you don't directly attach the customer gateway to the VPC</li>
 	<li><strong>VPN</strong> - This is the link that connects your Customer Gateway to your VPG:
<p id="bPAcJMM"><a href="http://codingbee.net/wp-content/uploads/2016/04/img_570c0813621c7.png"><img class="alignnone size-full wp-image-6909 pastedimages" src="http://codingbee.net/wp-content/uploads/2016/04/img_570c0813621c7.png" alt="" /></a></p>
</li>
</ul>
You can only attach 1 VPG to a VPC, however you can attach multiple Customer Gateways to a VPG, which is handy if you have several on premise sites, all with their own routers.

Internally the VPN has 2 connections to the VPG for better redundancies.

Another thing you need to do is that you need to update routing table inside your vpc as well as inside your on premise network, in order for the traffic to be routed correctly between on-premise and the vpc.
<h2>VPN Tunneling</h2>
This approach involves creating an EC2 instance that resides on a public subnet, inside this VPC. You then install a software called OpenVPN. The OpenVPN software comes in two parts the server side software and and client side. However both sides are installed by the same rpm package. On the EC2, you need to configure it as   an openvpn server.

On the on premise side, you set up your machines as open vpn clients. These clients can then use the openvpn's public IP address to establish a persistant vpn session (aka vpn tunnel) to the openvpn server.

The openvpn server then allocates an internal IP address to your openvpn client. This internal IP address isn't an IP address of any aws subnets, instead the openvpn server has an internal dhcp which aside this internal ip address to the openvpn client. This internal ip address is internally used by openvpn for routing purposes. The openvpn server has an internal route table that routes traffic coming from an openvpn client's internal ip address, and reroutes it to the permitted aws subnets (or the whole vpc).

Hence if on the openvpn client, you run:

<code>$ ssh username@{ec2-internal-ip-address}</code>

Here the openvpn client will intercept this request and route it the the openvpn server. The openvpn server will then use it's internal routing table to forward on the request to the target ec2 instance and relay responses back to the openvpn client.

This approach of setting up a vpn via openvpn, has a problem, which is that there's a single point of failure.

You can overcome this by having 3 EC2 instances for your openvpn setup:
<ul>
 	<li>main openvpn server - with an elasticip address attached</li>
 	<li>standby openvpn server - ideally in a different AZ</li>
 	<li>an EC2 instance that has a script that is constantly pinging the main openvpn server. If the pinging fails the it triggers an aws cli based script that detaches the elastic ip address form the main openvpn server and reattaches it to the standby openvpn server.</li>
</ul>
Note: you can take a similar approach to handling failover for nat instances.

Note, some routers have the openvpn client built in. This means that you can setup your router as a vpn client, and you don't need to setup openvpn clients on each of the devices that connects to the router, since that is all centralised by your onsite router which has a built in routing table to intercept internal IP bound traffic and reroute it the openvpn server.

&nbsp;

&nbsp;

&nbsp;

http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_VPN.html

http://docs.aws.amazon.com/AmazonVPC/latest/NetworkAdminGuide/Introduction.html