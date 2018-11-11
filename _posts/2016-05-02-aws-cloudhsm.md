---
ID: 578
post_title: 'AWS &#8211; CloudHSM'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-cloudhsm
published: true
post_date: 2016-05-02 00:00:00
---
CloudHSM (Hardware Security Module): This is essentially the name of a dedicated physical machine that is seperate from all the other AWS hardware, and it is used to store encryption keys. If an outside party gains access to these keys, then your AWS infrastructure is compromised. Hence even AWS employees don't have physical access to CloudHSM since they are locked in specially controlled rooms that is seperate from the rest of the AWS AZ's hardware.

These keys are only used from inside the CloudHSM device itself. Because of this, the CloudHSM is responsible for decrypting data it receives, and decrypting data it sends out. CloudHSM has an API that all your other AWS resources can interact with. All the AWS resources that can interact with CloudHSM are referred to as "CloudHSM clients". Therefore if our application needs data to be decrypted/encrypted then it interacts with CloudHSM via the api to get this done.

The CloudHSM devise has a lot of advanced logging feature to make it tamper resistant, and to let you know if it has been compromised.

&nbsp;

The whole concept of CloudHSM exists to satisfy vairous security requirements of certain industries, e.g. government security requirements, banking security requirements, and online retail compliance (<a href="https://www.pcicomplianceguide.org/pci-faqs-2/#1" rel="nofollow">PCI compliance</a>).

CloudHSM can be single point of failure, that's why you should have at least 2 CloudHSM devices, one in each AZ.

Here are some of the different types of keys CloudHSM can be used to store:
<ul>
 	<li>Filesystem encryption keys</li>
 	<li>database encryption keys</li>
 	<li>Digital Rights Management (DRM) related keys</li>
 	<li>S3 related encryption keys</li>
</ul>
&nbsp;

http://docs.aws.amazon.com/cloudhsm/latest/userguide/cloud-hsm-third-party-apps.html

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

https://aws.amazon.com/cloudhsm/