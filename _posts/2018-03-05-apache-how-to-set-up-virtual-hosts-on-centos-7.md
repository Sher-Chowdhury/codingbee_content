---
ID: 2824
post_title: 'Apache &#8211; How To Set Up Virtual Hosts on CentOS 7'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/open-source-software/apache-web-server/apache-how-to-set-up-virtual-hosts-on-centos-7
published: true
post_date: 2018-03-05 16:32:10
---
<strong>Virtual Hosts</strong> is one of Apache's most powerful and commonly used feature. Virtual Hosts (aka vhosts) let's allows you to host multiple websites on a single machine. There are 2 big advantages to this:

<ul>
	<li>Rather than needing one ip address per website, you know just need one ip address for the machine.</li>
	<li>A lot of machines capacity might not get used if it just hosts a single website. So having multiple websites on a single machine will make better use of your machine's computing capacity</li>
</ul>

There are a few ways to setup vhosts, but we'll walk through one of typical most common ways to create vhosts. In our example we have 2 websites that we want to host on our box, they are:

<ul>
	<li>example.com</li>
	<li>example.net</li>
</ul>


To start with we first need to create a content directory of each website, we'll choose:


<pre lang='bash'>
$ mkdir  /var/www/example_com
$ mkdir  /var/www/example_net

$ chown apache:apache /var/www/example_com
$ chown apache:apache /var/www/example_net

$ ll -Z /var/www/ | grep example
drwxr-xr-x. apache apache unconfined_u:object_r:httpd_sys_content_t:s0 example_com
drwxr-xr-x. apache apache unconfined_u:object_r:httpd_sys_content_t:s0 example_net

</pre>


Now let's create a dummy home page for each website:

<pre lang='bash'>
$ echo 'hello example.com' > /var/www/example_com/index.html
$ chown apache:apache /var/www/example_com/index.html

$ echo 'hello example.net' > /var/www/example_net/index.html
$ chown apache:apache /var/www/example_net/index.html

$ ls -lZ /var/www/example_com/index.html
-rw-r--r--. apache apache unconfined_u:object_r:httpd_sys_content_t:s0 /var/www/example_com/index.html
$ ls -lZ /var/www/example_net/index.html
-rw-r--r--. apache apache unconfined_u:object_r:httpd_sys_content_t:s0 /var/www/example_net/index.html
</pre>


Now we create the vhost config files, they are:

<pre lang='bash'>
$ ll /etc/httpd/conf.d | grep example
-rw-r--r--. 1 apache apache  243 Mar  5 19:11 example_com.conf
-rw-r--r--. 1 apache apache  242 Mar  5 19:10 example_net.conf

$ ll -Z /etc/httpd/conf.d | grep example
-rw-r--r--. apache apache unconfined_u:object_r:httpd_config_t:s0 example_com.conf
-rw-r--r--. apache apache unconfined_u:object_r:httpd_config_t:s0 example_net.conf


$ cat /etc/httpd/conf.d/example_com.conf
<VirtualHost *:80>
    ServerName www.example.com
    ServerAlias example.com
    DocumentRoot /var/www/example_com
    ErrorLog /var/log/httpd/example_com_error.log
    CustomLog /var/log/httpd/example_com_access.log combined
</VirtualHost>


$ cat /etc/httpd/conf.d/example_net.conf
<VirtualHost *:80>
    ServerName www.example.net
    ServerAlias example.net
    DocumentRoot /var/www/example_net
    ErrorLog /var/log/httpd/example_net_error.log
    CustomLog /var/log/httpd/example_net_access.log combined
</VirtualHost>
</pre>

We can test the syntax of these new config files using apachectl like this:

<pre lang='bash'>
$ apachectl configtest
Syntax OK
</pre>

We can also check whether httpd is aware of our new vhosts like this:

<pre lang='bash'>
$ httpd -D DUMP_VHOSTS
VirtualHost configuration:
*:80                   is a NameVirtualHost
         default server www.example.com (/etc/httpd/conf.d/example_com.conf:1)
         port 80 namevhost www.example.com (/etc/httpd/conf.d/example_com.conf:1)
                 alias example.com
         port 80 namevhost www.example.net (/etc/httpd/conf.d/example_net.conf:1)
                 alias example.net
*:443                  webserver.local (/etc/httpd/conf.d/ssl.conf:56)
</pre>



Since these are dummy websites for testing purposes, we don't have public dns entries for example.com and example.net. So we need to do the dns resolution locally using /etc/hosts file. In our example, our box's IP address is '10.0.5.10', so we add in the following lines:


<pre lang='bash'>
$ cat /etc/hosts
127.0.0.1	webserver.local	webserver
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
<strong>10.0.5.10   example.com</strong>
<strong>10.0.5.10   example.net</strong>
</pre>

Now we reload the configurations:


<pre lang='bash'>
$ systemctl restart httpd
</pre>


Finally we can test to see if this has worked:


<pre lang='bash'>
[root@webserver conf.d]# curl http://example.com
hello example.com
[root@webserver conf.d]# curl http://example.net
hello example.net
</pre>

Success!