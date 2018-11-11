---
ID: 405
post_title: Intro to SSL
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/uncategorized/intro-to-ssl
published: true
post_date: 2015-06-03 00:00:00
---
Really good guide:

https://jamielinux.com/docs/openssl-certificate-authority/index-full.html

Even better guide:

http://www.zytrax.com/tech/survival/ssl.html#single-cert

https://www.liberiangeek.net/2014/09/install-self-signed-ssl-certificate-nginx-cenos-7/

&nbsp;

https://www.digitalocean.com/community/tutorials/openssl-essentials-working-with-ssl-certificates-private-keys-and-csrs

The following will create a file called myblog.key
<pre>$ openssl genrsa -des3 -out /etc/nginx/ssl/myblog.key 2048</pre>
&nbsp;

This file is required to generate a csr file. Next we create the csr file:

$  openssl req -new -key /etc/nginx/ssl/myblog.key  -out myblog.csr

&nbsp;

http://operational.io/openssl-commonly-used-commands/

&nbsp;

http://en.wikipedia.org/wiki/Alice_and_Bob

https://www.google.co.uk/search?q=alice+and+bob+ssl&amp;ie=utf-8&amp;oe=utf-8&amp;gws_rd=cr&amp;ei=i6xyVeKwB-aa7gaXs4PAAg

If you have a website, e.g. your own wordpress blog and you want to set up ssl on it so that your url starts with http<span style="color: #ff0000;"><strong>s</strong></span>://... then you need to understand what is ssl is how it works.

&nbsp;

There are 3 main types of files when it comes to learning about ssl:
<ul>
 	<li>.csr file</li>
 	<li>.pem file</li>
 	<li>.crt</li>
</ul>
You can use the openssl command to create a ".csr" file. when creating the csr file you will get prompted to provide your name, address, email address....etc, but most importantly your website's url. The openssl command will then generate the ".csr" file.   "This file is essentially a request for a certificate" file.

You can then send this .csr file to a list of trusted private corprate companies. These group of comanies are often referred to as "Certificate Authorities", aka "CA".    You can view a list of private companies them from inside firefox:

<a href="http://codingbee.net/wp-content/uploads/2015/06/CA-list.png"><img class="alignnone size-full wp-image-4531" src="http://codingbee.net/wp-content/uploads/2015/06/CA-list.png" alt="CA-list" width="1076" height="895" /></a>

These companies have a file called a ".pem" file. this file is a highly secret and is tightly guarded. No one outside the company is allowed to have access of it, otherwise, it is a major security breach that will make it on national news.

Now we submit our csr file to a CA (any ca listed above will do)   and make a payment to them which can range from a few pounds to millions of pounds, depending on the CA and the level of protection you want. The CA will then use your csr along with it's internal pem file to generate a crt file. This "crt" file is then given back to you, and you place it on your website's server. You can refer to these as "server side crt files"

Your browser comes with included a list of crt files, from the above CA's. These crt files reside internally in the browser. You can think of these crt files as "ca certs".

&nbsp;

Now when your browser connects to your website using a "https" links, then the server will initially forward the server-side-crt file to the browser (laptop). The laptop will then feed that file along with the corresponding ca-cert file into a special highly complex algorithigm. If the result is a green light then the connection is established.

&nbsp;

On some websites, the server-side-crt file is not created by a CA that is listed in your browsers trusted CA list. In that case you will get the "confirm security exception" prompt.

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

https://www.digitalocean.com/community/tutorials/how-to-create-a-ssl-certificate-on-nginx-for-centos-6

http://www.cyberciti.biz/faq/linux-unix-nginx-redirect-all-http-to-https/
https://www.digitalocean.com/community/questions/how-do-i-rewrite-url-s-in-nginx-server-block (this one is better, I think.)

http://www.cyberciti.biz/faq/nginx-self-signed-certificate-tutorial-on-centos-redhat-linux/ (this might also be useful but never tried it)

https://www.digitalocean.com/community/tutorials/openssl-essentials-working-with-ssl-certificates-private-keys-and-csrs (this looks really useful)
here are the 2 files you need to edit
<pre>$ ls -l /etc/nginx/conf.d/ | grep -E 'default.conf$|ssl.conf$'
-rw-r--r-- 1 root root 1727 Jun  5 10:48 default.conf
-rw-r--r-- 1 root root  635 Jun  5 10:33 ssl.conf
</pre>
&nbsp;

Useful links:

<a href="http://prefetch.net/articles/checkcertificate.html">http://prefetch.net/articles/checkcertificate.html</a>   (this script is really useful to check if cert has expired)