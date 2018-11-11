---
ID: 325
post_title: 'Git &#8211; resetting Git.'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/git/git-resetting-git
published: true
post_date: 2015-03-18 00:00:00
---
If you want to drop all your local changes and commits, fetch the latest history from the server and point your local master branch at it like this
<pre>git fetch origin
git reset --hard origin/master</pre>


http://git-scm.com/blog/2011/07/11/reset.html