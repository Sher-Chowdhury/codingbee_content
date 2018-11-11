---
ID: 3347
post_title: >
  RHCE – Setting up login based
  authentication using Apache on
  CentOS/RHEL 7
author: sher
post_excerpt: >
  This is a step-by-step guide on setting
  up login based authentication using the
  authentication features provided by the
  Apache software (httpd)
layout: post
permalink: >
  https://codingbee.net/tutorials/rhce/rhce-setting-up-login-based-authentication-using-apache-on-centos-rhel-7
published: true
post_date: 2018-03-31 12:55:18
---
In the previous tutorial, we saw how we can host multiple websites on the same box using vhosts. Now we'll look at how we can configure Apache to password protect some web content. In Apache you can set up authentication so that when a user attempts to access a given folder's content, then they will get a prompt to enter a valid username and password. There's 2 ways to do this:

<ul>
	<li><strong>Setup user-based security</strong> - This is where you only give one user account access to restricted part of your website</li>
	<li><strong>Setup group-managed content</strong> - this is where you give a group of people access to a restricted part of your website</li>
</ul>

<em>Need to rewrite to article like this: first create 2 users tom and jerry. Then setup to only give tom access. Then modify it to give both tom and jerry access. then modify it again to make use of .htaccess</em>




<h2>Setup user-based security</h2>
In this approach, we set up authentication so that there is only one user account that's permitted to access the restricted content. In our example we'll take the vhost route to set this up. Let's say the restricted will reside in the folder /var/vhosts/example_com. So let's start with creating that directory. Then let's add some dummy content to it:

<pre lang='bash'>
$ mkdir /var/vhosts
$ mkdir /var/vhosts/example_com
$ chown apache:apache /var/vhosts
$ chown apache:apache /var/vhosts/example_com
$ echo 'TOP SECRET CONTENT for example.com' > /var/vhosts/example_com/index.html
$ chown apache:apache /var/vhosts/example_com/index.html
</pre> 

Now since we created a non-standard folder, we're likely to encounter SELinux problems, as indicated by the security contexts:


<pre lang='bash'>
$ ls -lZ /var | grep vhosts
drwxr-xr-x. apache apache unconfined_u:object_r:var_t:s0   vhosts
$ ls -lRZ /var/vhosts
/var/vhosts:
drwxr-xr-x. apache apache unconfined_u:object_r:var_t:s0   example_com

/var/vhosts/example_com:
-rw-r--r--. apache apache unconfined_u:object_r:var_t:s0   index.html
</pre>

Whereas the security context needs to be the same as that of /var/www/html:

<pre lang='bash'>
$ ls -lZ /var/www | grep html
drwxr-xr-x. root   root   system_u:object_r:httpd_sys_content_t:s0 html
</pre>


So we need to fix this, in particular the *_t (type). We fix this running the semanage command like this:


<pre lang='bash'>
$ semanage fcontext -at httpd_sys_content_t "/var/vhosts(/.*)?" 
</pre>

The asterisk in this expression means that this will get applied recursively to all child folders and files. For more info about this command, see <code>man httpd_selinux</code> which comes as part of the selinux-policy-doc rpm.

This policy has now been added in to SELinux's list policy. You can confirm this by running:

<pre lang='bash'>
$ semanage fcontext -l | grep '/var/vhosts'
/var/vhosts(/.*)?                                  all files          system_u:object_r:httpd_sys_content_t:s0
</pre>



Now we need to apply this policy to the folder and files we have created:

<pre lang='bash'>
$ restorecon -Rv /var/vhosts
restorecon reset /var/vhosts context unconfined_u:object_r:var_t:s0->unconfined_u:object_r:httpd_sys_content_t:s0
restorecon reset /var/vhosts/example_com context unconfined_u:object_r:var_t:s0->unconfined_u:object_r:httpd_sys_content_t:s0
restorecon reset /var/vhosts/example_com/index.html context unconfined_u:object_r:var_t:s0->unconfined_u:object_r:httpd_sys_content_t:s0
</pre>
Here we are doing this (R)ecursively and (v)erbosely. 

Now if we check again, everything looks better:


<pre lang='bash'>
$ ls -lZ /var | grep vhosts
drwxr-xr-x. apache apache unconfined_u:object_r:httpd_sys_content_t:s0 vhosts
$ ls -lRZ /var/vhosts
/var/vhosts:
drwxr-xr-x. apache apache unconfined_u:object_r:httpd_sys_content_t:s0 example_com

/var/vhosts/example_com:
-rw-r--r--. apache apache unconfined_u:object_r:httpd_sys_content_t:s0 index.html
</pre>

Next we create the following vhost file to point to our new web content directory:

<pre lang='bash'>
$ ll /etc/httpd/conf.d/example_com.conf
-rw-r--r--. 1 apache apache 343 Mar  5 21:41 /etc/httpd/conf.d/example_com.conf

$ cat /etc/httpd/conf.d/example_com.conf
<VirtualHost *:80>
    ServerName www.example.com
    ServerAlias example.com
    DocumentRoot /var/vhosts/example_com

    <Directory /var/vhosts/example_com>
        AllowOverride AuthConfig
    </Directory>


    ErrorLog /var/log/httpd/example_com_error.log
    CustomLog /var/log/httpd/example_com_access.log combined
</VirtualHost>
</pre>


I also used apachectl command to confirm that there are no syntax errors. In this file we indicated that we are allowing the .htaccess file to set up the authentication. Note, we could avoid using .htaccess and instead do everything straight inside the Directory directive. However I'm using .htaccess on this occasion just to show .htaccess in action. So next I created the .htaccess in the DocumentRoot:

<pre lang='bash'>
$ ls -l /var/vhosts/example_com/.htaccess
-rw-r--r--. 1 apache apache 142 Mar  5 22:07 /var/vhosts/example_com/.htaccess

$ cat /var/vhosts/example_com/.htaccess
AuthType Basic
AuthName "Please enter a Username and Password"
AuthUserFile "/etc/httpd/conf/.AuthUserFile_for_example_com"
Require user james
</pre>
By convention and best practice, the AuthUserFile should be a hidden file.  

Now we need to generate this user 'james', which is done using the htpasswd command:

<pre lang='bash'>
$ htpasswd -c /etc/httpd/conf/.AuthUserFile_for_example_com james
New password:
Re-type new password:
Adding password for user james
</pre>

In my case, I set the password to 'password123'. This ends up (c)reating the following file:

<pre lang='bash'>
$ ll /etc/httpd/conf/.AuthUserFile_for_example_com
-rw-r--r--. 1 root root 44 Mar  6 09:38 /etc/httpd/conf/.AuthUserFile_for_example_com

$ cat /etc/httpd/conf/.AuthUserFile_for_example_com
james:$apr1$I8OrGSri$7SY9/5NIMgv1CTIZ6N.eU/
</pre>




Now we need to restart httpd:

<pre lang='bash'>
$ systemctl restart httpd</pre>

Next, since we don't have a public dns for example.com, we need to add a local entry in our hosts file:

<pre lang='bash'>
$ cat /etc/hosts | grep example.com
10.0.5.10   example.com
</pre>

Now we can test this by using chrome/firefox/elinks:

<pre lang='bash'>
$ elinks http://example.com
</pre>

Or you can just test this using curl:


<pre lang='bash'>
$ curl http://example.com
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>401 Unauthorized</title>
</head><body>
<h1>Unauthorized</h1>
<p>This server could not verify that you
are authorized to access the document
requested.  Either you supplied the wrong
credentials (e.g., bad password), or your
browser doesn't understand how to supply
the credentials required.</p>
</body></html>


$ curl -u james:password123 http://example.com
TOP SECRET CONTENT for example.com

</pre>

At this point our website is now password protected. However we should also be using the https protocol, not http. We'll cover this later. 

<h2>Setup group-managed content</h2>
So far we have looked at how to password protect a web content with a single login credential. However another common scenarios is to give a group of users access to some restricted web content. Let's say we want the content of the the website http://example.net to be accessible by only 2 users, 'Tom' and 'Jerry'. To take this approach, we will again take the vhost and .htaccess approach. So let's create our vhost folder, and put a index.html file in it:


<pre lang='bash'>
$ mkdir /var/vhosts/example_net
$ chown apache:apache /var/vhosts
$ chown apache:apache /var/vhosts/example_com
$ echo 'SUCCESS!, you have accessed SECRET data that only a small group of users are allow to view in example.net' > /var/vhosts/example_net/index.html
$ chown apache:apache /var/vhosts/example_net/index.html
</pre>


Lets check the SELinux contexts:


<pre lang='bash'>
$ ls -lZ /var/vhosts/ | grep example_net
drwxr-xr-x. root   root   unconfined_u:object_r:httpd_sys_content_t:s0 example_net
$ ls -lRZ /var/vhosts/example_net
/var/vhosts/example_net:
-rw-r--r--. apache apache unconfined_u:object_r:httpd_sys_content_t:s0 index.html
</pre>

This time the type attribute within the SELinux is correct, i.e. it is set to 'httpd_sys_content_t'. That's because we already set the recursive SELinux policy at the /var/vhost level earlier on in this article when we were setting up the single user based access. 

Now in order to give Tom and Jerry access, they also have to exist as OS level users on the box itself, so we do:


<pre lang='bash'>
$ useradd tom
$ useradd jerry
</pre>

We also need to create a OS level group, which we will then whitelist in the apache setup. Let's call this group 'example_net_admins'. Then we need to add tom and jerry to that group:


<pre lang='bash'>
$ groupadd example_net_admins

$ usermod -aG example_net_admins tom
$ usermod -aG example_net_admins jerry

# let's confirm this has worked:
$ cat /etc/group | grep example_net_admins
example_net_admins:x:1005:tom,jerry
</pre>

Now we create the vhost config file:


<pre lang='bash'>
$ ll /etc/httpd/conf.d/example_net.conf
-rw-r--r--. 1 apache apache 342 Mar  6 11:15 /etc/httpd/conf.d/example_net.conf

$ cat /etc/httpd/conf.d/example_net.conf
<VirtualHost *:80>
    ServerName www.example.net
    ServerAlias example.net
    DocumentRoot /var/vhosts/example_net

    <Directory /var/vhosts/example_net>
        AllowOverride AuthConfig
    </Directory>


    ErrorLog /var/log/httpd/example_net_error.log
    CustomLog /var/log/httpd/example_net_access.log combined
</VirtualHost>

</pre>

Now let's create the .htaccess file:

<pre>
$ ll /var/vhosts/example_net/.htaccess
-rw-r--r--. 1 root root 231 Mar  6 11:35 /var/vhosts/example_net/.htaccess
$ cat /var/vhosts/example_net/.htaccess
AuthType Basic
AuthName "Please enter a Username and Password"
AuthUserFile "/etc/httpd/conf/.group_access_passwd_db_for_example_net"
<strong>AuthGroupFile</strong> "/etc/httpd/conf/.allowed_groups_for_example_net"
Require <strong>group</strong> example_net_admins
</pre>

Notice this time we used the <a href="https://httpd.apache.org/docs/2.4/mod/mod_authz_groupfile.html#authgroupfile">AuthGroupFile</a> directive in addition to the AuthUserFile. 


Next we need to create the 2 files referenced in the .htaccess file. 


<pre lang='bash'>
$ ll /etc/httpd/conf/.allowed_groups_for_example_net
-rw-r--r--. 1 apache apache 30 Mar  6 11:42 /etc/httpd/conf/.allowed_groups_for_example_net

$ cat /etc/httpd/conf/.allowed_groups_for_example_net
example_net_admins: tom jerry
</pre>

Now we create the password file:


<pre lang='bash'>
$ htpasswd -c /etc/httpd/conf/.group_access_passwd_db_for_example_net tom
New password:
Re-type new password:
Adding password for user tom
</pre>

here we used the (c)reate option to not only set the password for user tom, but also create the file itself. There when we set the password for the next user, we omit the (c)reate option otherwise it would end up deleting the existing file, and recreating it from scratch. So we do:


<pre lang='bash'>

$ htpasswd /etc/httpd/conf/.group_access_passwd_db_for_example_net jerry
New password:
Re-type new password:
Adding password for user jerry
</pre>

Now all that's left doing is restarting the httpd daemon:

<pre lang='bash'>
$ systemctl restart httpd
</pre>
 
Now we are ready to test this setup. so first we need to add a local entry in our hosts file (our box's ip address is):


<pre lang='bash'>
$ cat /etc/hosts | grep example.net
10.0.5.10  example.net
</pre>


Then test with firefox/chrome/elinks/curl:

<pre>
$ curl http://example.net
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>401 Unauthorized</title>
</head><body>
<h1>Unauthorized</h1>
<p>This server could not verify that you
are authorized to access the document
requested.  Either you supplied the wrong
credentials (e.g., bad password), or your
browser doesn't understand how to supply
the credentials required.</p>
</body></html>


$ curl -u tom:WrongPassword http://example.net
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>401 Unauthorized</title>
</head><body>
<h1>Unauthorized</h1>
<p>This server could not verify that you
are authorized to access the document
requested.  Either you supplied the wrong
credentials (e.g., bad password), or your
browser doesn't understand how to supply
the credentials required.</p>
</body></html>

$ curl -u tom:cat http://example.net
SUCCESS!, you have accessed SECRET data that only a small group of users are allow to view in example.net

$ curl -u jerry:mouse http://example.net
SUCCESS!, you have accessed SECRET data that only a small group of users are allow to view in example.net
</pre>

Note in our example we set the password for tom as 'cat' and for jerry we set it as 'mouse'. 

[post-content post_name=rhsca-quiz] 

<strong>Quiz about Setting up user-managed content:</strong>

[accordion]
[toggle title="What are the various ways to password protect web content?"]
<ul>
	<li><strong>Setup user-based security</strong> - Give only one user account access to restricted part of your website</li>
	<li><strong>Setup group-managed content</strong> - Give a group of people access to a restricted part of your website</li>
</ul>
[/toggle]
[toggle title="What are the commands to make the 'hello world' file /var/vhosts/example_com/index.html that can be accessible by the web server (ignore SELinux for now)?"]
$ mkdir -p /var/vhosts/example_com
$ echo 'hello world' > /var/vhosts/example_com/index.html
$ chown -R apache:apache /var/vhosts
[/toggle]
[toggle title="What is the command to view file contexts of /var/www and /var/vhosts/?"]
$ ll -dlZ /var/www
drwxr-xr-x. root root system_u:object_r:httpd_sys_content_t:s0 /var/www
$ ll -dlZ /var/vhosts/
drwxr-xr-x. apache apache unconfined_u:object_r:var_t:s0   /var/vhosts
[/toggle]
[toggle title="What is the command to add a new policy rule to the SELinux manual?"]
$ semanage fcontext -at httpd_sys_content_t "/var/vhosts(/.*)?"
[/toggle]
[toggle title="What is the command to view this new rule in the manual?"]
$ semanage fcontext -l | grep '/var/vhosts'
/var/vhosts(/.*)?    all files          system_u:object_r:httpd_sys_content_t:s0
[/toggle]
[toggle title="What is the command to apply this rule to /var/vhosts?"]
$ restorecon -Rv /var/vhosts
[/toggle]
[toggle title="What vhost file do you need to create and what will it's content be, to give access to single user called james (non .htaccess approach)?"]
<pre lang='bash'>
$ cat /etc/httpd/conf.d/example_com.conf
<VirtualHost *:80>
  ServerName www.example.com
  ServerAlias example.com
  ServerAdmin sher@example.com
  DocumentRoot /var/vhosts/example_com
  ErrorLog   /var/log/httpd/example_com_error_log
  CustomLog /var/log/httpd/example_com_access.log combined

  <Directory "/var/vhosts/example_com">
    AuthType Basic
    AuthName "Please enter a Username and Password"
    AuthUserFile "/etc/httpd/conf/.AuthUserFile_for_example_com"
    Require user james
  </Directory>
</VirtualHost>
</pre>

[/toggle]
[toggle title="What is the command to generate a password for the 'james' user?"]
$ htpasswd -c /etc/httpd/conf/.AuthUserFile_for_example_com james
[/toggle]
[toggle title="What 2 checks you can perform to check the vhost configs?"]
$ httpd -d
# and 
$ httpd -D DUMP_VHOSTS
[/toggle]
[toggle title="Now what do you need to do??"]
restart the httpd daemon. 
[/toggle]
[toggle title="What do you need to do to test this setup?"]
- updated /etc/hosts file
- use elinks   # not very reliable. Better to use curl. 
[/toggle]
[toggle title="What is the curl command to test this?"]
$ curl -u james:password123 http://example.com
[/toggle]
[toggle title="What is the .htaccess equivalent to this?"]
# create the file: 
$ cat /var/vhosts/example_com/.htaccess
AuthType Basic
AuthName "Please enter a Username and Password"
AuthUserFile "/etc/httpd/conf/.AuthUserFile_for_example_com"
Require user james

# also replace content inside directory block directive to 'AllowOverride authconfig' 
</pre>
[/toggle]
[/accordion]

<strong>Quiz about Setting up group-managed content </strong>

[accordion]

[toggle title="what are the commands to create to system users tom and jerry and add them to a group called examle_com_admin?"]
$ groupadd example_com_admin
$ useradd tom
$ useradd jerry
$ usermod -aG example_com_admin tom
$ usermod -aG example_com_admin jerry
[/toggle]
[toggle title="What does the vhost file needs to look (non .htaccess approach)?"]
$ cat /etc/httpd/conf.d/example_com.conf
<VirtualHost *:80>
  ServerName www.example.com
  ServerAlias example.com
  ServerAdmin sher@example.com
  DocumentRoot /var/vhosts/example_com
  ErrorLog   /var/log/httpd/example_com_error_log
  CustomLog /var/log/httpd/example_com_access.log combined

  <Directory "/var/vhosts/example_com">
    AuthType Basic
    AuthName "Please enter a Username and Password"
    AuthUserFile "/etc/httpd/conf/.group_access_passwd_db_for_example_com"
    AuthGroupFile "/etc/httpd/conf/.allowed_groups_for_example_com"
    Require group example_com_admin
  </Directory>
</VirtualHost>
[/toggle]
[toggle title="What is the content of AuthGroupFile?"]
$ cat /etc/httpd/conf/.allowed_groups_for_example_com
example_com_admin: tom jerry
[/toggle]
[toggle title="How do you create the AuthUserFile?"]
$ htpasswd -c /etc/httpd/conf/.group_access_passwd_db_for_example_com tom
$ htpasswd /etc/httpd/conf/.group_access_passwd_db_for_example_com jerry
[/toggle]
[toggle title="now what do you need to do?"]
$ httpd -t
$ httpd -D DUMP_VHOSTS
$ systemctl restart httpd
[/toggle]

[/accordion]