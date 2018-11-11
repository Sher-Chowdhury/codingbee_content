---
ID: 431
post_title: 'SELinux &#8211; Common SELinux network service related problems'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-common-selinux-network-service-related-problems
published: true
post_date: 2015-06-22 00:00:00
---
SELinux has it's own internal database that stores information about most commonly used network based software packages, e.g. ssh, apache, nfs,....etc. As a result it knows what default (aka standard) ports these service listen on (e.g. port 80 and 443 for apache) and where the default directories are located (e.g. /var/www/html for apache). Therefore for network based services like httpd, SELinux may prevent it from working properly if configure your software to use a:

<ul>
	<li>non-standard directory - e.g. for apache you set the data folder to be /var/www/my-website instead of the standard /var/www/html directory</li>

	<li>non-standard port number - e.g. for apache you set the default port number to be 8102 instead of default port number of port 80 or 443.  </li>
</ul>


<h2>Identifying standard ports</h2>

There's two places to find this:

<pre>
$ cat /etc/services
</pre>

This file is just for information. As for the which ports selinux will allow apache to listen on, we can find this by running:


and also like this:

<pre>
$ semanage port -l | grep http
http_cache_port_t              tcp      8080, 8118, 8123, 10001-10010
http_cache_port_t              udp      3130
<strong>http_port_t                    tcp      80, 81, 443, 488, 8008, 8009, 8443, 9000</strong>
pegasus_http_port_t            tcp      5988
pegasus_https_port_t           tcp      5989
</pre>





In these scenarios, you need to inform SELinux that you want to use custom port number and directories. You do that by adding policy rules. For custom port numbers, you will need to run a command like this:


<pre>
$ semanage -port -a -t http_port_t -p tcp {custom port number}
</pre>

Note: sealert should be able to suggest this.