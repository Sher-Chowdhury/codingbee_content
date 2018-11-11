---
ID: 556
post_title: 'Linux &#8211; The dig command'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/uncategorized/linux-the-dig-command
published: true
post_date: 2016-04-02 00:00:00
---
You can use the nslookup command to find what ip address a url resolves to. But another way to do this is by using the dig command:
<pre>$ dig google.com +short
{list of ip address}
</pre>
This gives a list of ip addresses, since google.com website is load-balanced across several servers.

To find which dns servers gave this resolution info, do:
<pre>$ dig google.com ns +short
{list of urls}
</pre>
These are the urls of dns servers.

If you want to find out what a server's fqdn is, then do:
<pre>$ dig -x {ip-addr}</pre>
This will give the same info that you would get if you had ssh'd into the server and ran the 'hostname' command.


Also you can specify what dns server to use when using dig:

<pre>
$ dig https://example.com @8.8.8.8 
</pre>

This is using google's dns. If you want to use your company's internal dns, then do:

<pre>
$ dig https://example.com @{internal-dns} 
</pre>