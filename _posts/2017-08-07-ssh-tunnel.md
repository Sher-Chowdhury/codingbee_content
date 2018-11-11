---
ID: 2272
post_title: ssh tunnel
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/linux/ssh-tunnel
published: true
post_date: 2017-08-07 13:36:31
---
Create an ssh tunnel like this:

<pre>
$ ssh -L 8081:localhost:80 {fqdn}
</pre>

Then access the server's website using:

http://localhost:8081