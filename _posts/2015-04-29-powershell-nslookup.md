---
ID: 364
post_title: 'PowerShell &#8211; nslookup'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-nslookup
published: true
post_date: 2015-04-29 00:00:00
---
If you want to check if machines fqdn is mapped to a ip address in dns, then on a windows machine you do:


start->enter "nslookup"

then type domain name, and hit enter, e.g.:


You should get an output like this:

<pre>
> google.com
Non-authoritative answer:
Name:    google.com
Addresses:  2a00:1450:4009:800::200e
          216.58.210.46
>
</pre>