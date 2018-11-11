---
ID: 524
post_title: 'AWS &#8211; Identity Access Management (IAM)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-identity-access-management-iam
published: true
post_date: 2016-01-17 00:00:00
---
<h2>Identity Access Management (IAM)</h2>
IAM is a service that lets you
<ul>
	<li>create child aws user accounts,</li>
	<li>create groups, and organise the child accounts into groups</li>
	<li>set user and group permissions on what they are allowed/denied to do.</li>
</ul>
IAM works at the very top AWS level. This means that it lets us create users at the global level, i.e. when you create a user, then that user automatically exists in all regions.

&nbsp;

It lets you set permissions for resources that belong to the following service categories:
<ul>
	<li>computing</li>
	<li>storage</li>
	<li>databases</li>
	<li>applications</li>
</ul>
These permissions are set specified against particular API-calls/CLI-options/web-gui-console

Here are some examples:
<ul>
	<li>give a user (or group) permission to create new EC2 instances</li>
	<li>deny user (or group) permssions to delete an particular EC2 instance</li>
	<li>Give user (or group) access to resources in specific regions</li>
	<li>Give user (or group) access to resources in specific Availability Zones</li>
</ul>
&nbsp;

IAM is very granular and let's you set all kinds of permissions.

Main benefits of IAM are:
<ul>
	<li>Centralized control who which users/groups can interact with what resources</li>
	<li>Customised billing for user or groups, e.g. a group only pays for the resources it has used. Good way to pay for IT project budgets to fund AWS usage.</li>
	<li>restrict access based on originating IP address. E.g. you can't log into aws unless you are connected to your company's network via cable or wifi. This is achieved by specifying ip ranges.</li>
	<li>Let's you manage security credentials - e.g. API access keys.</li>
	<li>  Provide temporary user access as and when necessary - e.g. <a href="http://docs.aws.amazon.com/STS/latest/APIReference/Welcome.html" target="_blank">simple token service</a>.</li>
	<li>Integrate with Microsoft Active Directorym which means you don't have to manually create IAM user accounts, and also achieve single-sign-on.</li>
	<li>Create roles, (e.g. read-only roles) with specific permissions. then attach that role user IAM user accounts or groups.</li>
	<li>You can even assign roles to a resource, e.g. EC2 instance, so that it can access the AWS API like a user.</li>
	<li>You can specify password policy (e.g. password must contain at least one digit)</li>
	<li>Set Multi-Factor Authentication on a per user basis. This could mean senior admin user must own a calculator like device that generates a pin to use to login, on top of username+password. This is commonly used in banks.</li>
	<li>Provide pre-designed policy templates out of the box, e.g.:
<ul>
	<li>Admin access</li>
	<li>Power user access - Can do everything accept manage IAM</li>
	<li>Read only access - E.g. for project managers to monitor how much AWS is costing.</li>
</ul>
</li>
</ul>
&nbsp;

In AWS, the "root user" account is the email address you used to create your AWS account. It is best practice not to use this account for day to day task. Instead you should create an "admin" IAM user account asap, then use that account from that point forward.

By default, a user is denied access to everything, until they are allocated permission to access what you allow them to access.

It is best practice to never store or pass security credentials on  EC2 instances. Instead IAM does the authentication for you, in the form of IAM roles. It's similar to how <a href="http://software.dell.com/solutions/privileged-management/" rel="nofollow">PAM</a> works. So in practice it will be a bit like opening up a putty session, and entering the hostname, then you log in automatically with your AD credentials without needing type in your username/password.

In other words, IAM roles is a bit like single-sign-on when logging into an EC2 instance.

&nbsp;
<h2>Groups and Roles</h2>
Lets say you    want to grant a specific permission (e.g. create ec2 instance) to a 100 users. Then you can do this by adding it to each user one at a time. Now let's say a couple of weeks later, you want to grant another permission to these 100 users. Once again you would need to assign each user one at a time.   This can get tedious.

A better way is to create a group (with an appropriate name), and then add the 100 users to this group. After that you can then assign permissions to the group as a whole, rather then individually. You can also assign even more permissions to the individuals of the group as well. E.g. if one member in the group is the team leader who requires higher priveleges.

Let's now say that you don't have just one group, but actually 25 groups. Now let's say all these groups needs to have the same set of 15 different permissions. Then adding each permission to each group can get tedious.

A better approach is to group together all the permissions. This grouping of permissions is called a   "role". You need to give this role an appropriate name, e.g. "developers". You can then assign this role to each of the groups.

An IAM <span class="emphasis"><em>role</em></span>  is a series  of permission  policies that are grouped together.

This means that if in a couple of weeks, a new permission needs to be added/revoked from all the groups, then instead of editing this for each group, you can instead edit the single role.

Using groups and roles gives you complete flexibility in how you manage users and permissions:
<ul>
	<li>You can still add individual permissions to a user</li>
	<li>You can still add individual permissions to a group</li>
	<li>a user can be a member of one or more groups</li>
	<li>you can add one or more roles to a group</li>
	<li>you can add one or more roles to a user</li>
</ul>
&nbsp;

&nbsp;

The concept of Groups and Roles can also be applied to resources, e.g. EC2 instances. E.g. you can a cluster of ec2 instance to a group called "projectx", and then assign a role called "projectx-db-access" to this group. This then gives all the ec2 instances access to the RDS service.

&nbsp;

Using roles means that your EC2 instances doesn't need to store any api-keys/login-credentials. Instead If a user logins into AWS and is authenticated via IAM, then the user will then get the role that specifies what instances it has permission  to access.

Sometimes you might want to give users AWS access but their credentials are stored in Active Directory that is outside of AWS,  or, you might want to give AWS access to third parties so that they can perform an audit on your resources.

For these scenarios, you can delegate access to AWS resources using an <span class="emphasis"><em>IAM role</em></span>. This section introduces roles and the different ways you can use them, when and how to choose among approaches, and how to create, manage, switch to (or assume), and delete roles.

http://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html

&nbsp;

&nbsp;

&nbsp;

&nbsp;

http://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html

&nbsp;
<h2></h2>