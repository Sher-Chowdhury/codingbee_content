---
ID: 514
post_title: 'AWS &#8211; Amazon ElastiCache'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-amazon-elasticache
published: true
post_date: 2016-01-16 00:00:00
---
Databases can often get the same query to proces, and each time it returns the same output over and over again. This can slow down the db's performance.

To overcome this, you can use a caching service. This service will intercept the repetitive queries and send back the cached output. The database will keep the cacheing service up to date with the latest data.

&nbsp;

There are   2 main caching software, they are:
<ul>
	<li><a href="http://redis.io/" rel="nofollow">redis</a></li>
	<li><a href="http://memcached.org/" rel="nofollow">memcached</a></li>
</ul>
Amazon ElastiCache is a fully managed in-memory cache engine. Once again, you can't access the underlying OS that the cache engine is running on. You can set up Amazon ElastiCache to run using either of above 2 caching engines only.

Note, your application needs to be written in a way to support Redis/Memcached.

&nbsp;

&nbsp;

&nbsp;

https://aws.amazon.com/elasticache/

&nbsp;

&nbsp;

&nbsp;

https://github.com/theforeman/foreman_memcache