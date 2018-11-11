---
ID: 591
post_title: puppet performance tuning
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/uncategorized/puppet-performance-tuning
published: true
post_date: 2016-11-28 00:00:00
---
The latest version of PE 2016.4 has the capability to monitor the heap memory as a feature of puppet server 2.6.

https://docs.puppet.com/puppetserver/2.6/status-api/v1/services.html#example-request-and-response-for-a-debug-level-get-request

this guide:

https://puppet.com/blog/puppet-server-advanced-memory-debugging



https://docs.puppet.com/pe/latest/install_multimaster.html

https://support.puppet.com/hc/en-us/articles/225049688


https://docs.puppet.com/puppetserver/latest/tuning_guide.html#number-of-jrubies



Thundering herd test:


After you’ve added hundreds of nodes to your deployment you may notice that your agents are running slow or timing out. When hundreds of nodes check in simultaneously to request a catalog, it might cause a so-called thundering herd of processes that causes CPU and memory performance to suffer. To verify that you have a thundering herd condition, you can run a query on the PuppetDB node (the master in a monolithic installation) to show how many nodes check in per minute.

Log into the PuppetDB node(the master in a monolithic installation) as the pe-postgres user.

Open the PostgreSQL command line interface by running sudo su - pe-postgres -s /bin/bash -c "psql -d pe-puppetdb".

Find out how many nodes are checking in per minute for one hour by running the following query, replacing with a one hour period in the recent past:

select date_part('minute', start_time), count(*) from reports where start_time between '<DATE AND TIME>' and '<DATE AND TIME>' GROUP BY date_part('minute', start_time) ORDER BY date_part('minute', start_time) ASC;

For example, the following example shows a query for December 23rd, 2015 between 5:30 PM to 6:30 PM:

select date_part('minute', start_time), count(*) from reports where start_time between '2015-12-03 17:30:00' and '2015-12-03 18:30:00' GROUP BY date_part('minute', start_time) ORDER BY date_part('minute', start_time) ASC;

Check the results to see if you have a consistent issue with many nodes checking in simultaneously by running the query several times using different time spans.

Exit the PostgreSQL command line by typing \q.

If you find that you have a thundering herd condition, distribute agent checkins more evenly by using splay, fqdn_rand. 

https://docs.puppet.com/puppet/latest/reference/configuration.html#splay

Then confirm that any changes you make are effective by re-running the query in step 3.



e.g.

pe-puppetdb=# select date_part('minute', start_time), count(*) from reports where start_time between '2016-11-21 09:00:00' and '2016-11-21 10:00:00' GROUP BY date_part('minute', start_time) ORDER BY date_part('minute', start_time) ASC; 
date_part | count 



if puppetmaster cpu is overloaded, then it will show up in puppetserver.log as the word 'overloaded', e.g.:

2016-11-22 11:48:54,110 ERROR [qtp2038037765-49147] [p.p.jruby-request] Error 503 on SERVER at /puppet/v3/file_metadata/plugins: Attempt to borrow a JRuby instance from the pool timed out; Puppet Server is temporarily overloaded. If you get this error repeatedly, your server might be misconfigured or trying to serve too many agent nodes. Check Puppet Server settings: jruby-puppet.max-active-instances.



https://github.com/npwalker/pe_metric_curl_cron_jobs


Also you need to check whether puppetdb is storing large files, you can do this using this handy script:

https://github.com/ripienaar/puppet-reportprint


To gather per minute compilation statistics for catalogs you can run the following command on the puppetserver.log

$ grep Compiled puppetserver.log  | awk -F ':' '{print $1, $2}' | sort | uniq -c


https://docs.puppet.com/puppetdb/latest/scaling_recommendations.html


Avoid enabling the hiera deep merge setting, instead use:

https://docs.puppet.com/puppet/latest/reference/function.html#hieraarray
https://docs.puppet.com/puppet/latest/reference/function.html#hierahash

This will speed up catalog compile times. 


monolythic+compile master setup is the recommended approach. It's easier to maintain, since puppetdb, web-console, and puppetmaster are all installed on the same box, but also easier to scale horizontally by building new puppetmasters.