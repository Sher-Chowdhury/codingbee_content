---
ID: 371
post_title: 'RHCSA &#8211; Memory usage'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-memory-usage
published: true
post_date: 2015-05-02 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is the command too see how much ram is free/used?"]
$ free -m
[/toggle]
<hr/>



If you want to see how much ram your machine has installed and how much of it is being used, then you need to use the "free" command:


<pre>
$ free -m
              total        used        free      shared  buff/cache   available
Mem:            993         266         440           6         286         573
Swap:          2047           0        2047

</pre> 


We used the -m option to output the info in megabytes. 


In this case we have about 1gb of ram installed, of which 266MB is being used and 440MB is free.