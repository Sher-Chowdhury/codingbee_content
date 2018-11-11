---
ID: 2319
post_title: Using sdiff like a pro
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/linux/using-sdiff-like-a-pro
published: true
post_date: 2017-08-17 11:04:00
---
My colleague <a href="https://www.linkedin.com/in/kiran-patel-683055101/" rel="nofollow">Kiran Patel</a>, has showed me this really cool command:

<pre>
superdiffcommand='systemctl list-units --type=service | grep running' && sdiff -w 200 <(ssh box1.com $superdiffcommand) <(ssh box2.com $superdiffcommand)
</pre>

If the command you want to run requires root priveleges, then you can do:


<pre>
superdiffcommand='sudo systemctl list-units --type=service | grep running' && sdiff -w 200 <(ssh -t box1.com $superdiffcommand) <(ssh -t box2.com $superdiffcommand)
</pre>