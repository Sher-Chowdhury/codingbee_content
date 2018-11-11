---
ID: 533
post_title: 'AWS &#8211; IAM Groups and Roles'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-iam-groups-and-roles
published: true
post_date: 2016-02-06 00:00:00
---
<h2>Groups and Roles</h2>
Roles are universal. I.e. if you create it in one region, it becomes in all other regions.

&nbsp;

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

Note: you can only assign a role to an EC2 instance, at the time of creating the that instance. That mean's that you can't assign/change a role to an existing EC2 instance. Furthermore, you can only assign a maximum of one IAM role to an instance.

&nbsp;

&nbsp;

Sometimes you might want to give users AWS access but their credentials are stored in Active Directory that is outside of AWS,  or, you might want to give AWS access to third parties so that they can perform an audit on your resources.

For these scenarios, you can delegate access to AWS resources using an <span class="emphasis"><em>IAM role</em></span>. This section introduces roles and the different ways you can use them, when and how to choose among approaches, and how to create, manage, switch to (or assume), and delete roles.

&nbsp;

&nbsp;

&nbsp;
<h2>Permission conflicts</h2>
By default, a newly created user is implicitly denied access to everything, and you need to explicitly grant priveleges to the user by directly assigning permission policies, or indirectly by assigning users to groups and roles.

However in doing so you might end up with possible conflicts, e.g. a user is a member of 2 groups, the first group is allowed to delete EC2 instances, whereas in the second group, they are explicitly denied the ability of delete EC2 instances. When there is a conflict like this, then the deny permissions have the final say.

This is handy, if you suspect one of your users account has been hacked. In this situation, instead of removing all the groups and roles from the user, you can instead apply a "deny-all" permission, on top, which will override all other allow permissions.

&nbsp;

&nbsp;

&nbsp;
<h2>Useful tips</h2>
A common thing people will want to do using be able to:
<ul>
 	<li>run aws cli commands from inside an EC2 instance</li>
 	<li>access to s3 buckets</li>
 	<li>passwordless ssh access to bitbucket/github</li>
</ul>
&nbsp;

with respect to running aws cli commands, your first ideas could be:
<ul>
 	<li>create an ami wiht the ~/.aws/credentials files backed in. The downside to this approach is  ami becomes useless if those credentials are updated.</li>
 	<li>create a user data (cloud-init) script that generates the  ~/.aws/credentials file during instance creation time. However the downside to this approach is that you want to keep your user-data as short as possible and this will add extra bloat. Also not very elegant approach.</li>
</ul>
The best approach is to attach an IAM role to the instance, which has API gateway access privileges.

http://docs.aws.amazon.com/apigateway/latest/developerguide/permissions.html

&nbsp;

As for the S3 access, you can take the same route as the API issue. Just give the IAM role access to the relevant S3 buckets.

&nbsp;

As for the Bitbucket/Github scenario, just store the githubs/bitbucket's private id_rsa in S3. Add IAM S3 privileges as described above. Then download this id_rsa key via user-data (cloud data) shell script, during the EC2 instance's creation.

&nbsp;

Also checkout:

https://forge.puppet.com/modules?utf-8=%E2%9C%93&amp;sort=rank&amp;q=s3

&nbsp;

&nbsp;

&nbsp;

http://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html

&nbsp;

&nbsp;

&nbsp;

&nbsp;

http://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html

&nbsp;
<h2></h2>