---
ID: 474
post_title: 'Bash &#8211; If-Statement based on exit codes'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/uncategorized/bash-if-statement-based-on-exit-codes
published: true
post_date: 2015-10-29 00:00:00
---

<pre>
{some command}
if [ $? -eq 0 ]; then
  ...
else
  ...
fi
</pre>