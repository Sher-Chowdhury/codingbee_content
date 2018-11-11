---
ID: 589
post_title: 'Puppet &#8211; querying puppetdb with postgres command line (psql)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-querying-puppetdb-with-postgres-command-line-psql
published: true
post_date: 2016-09-20 00:00:00
---
<h2>Exported Resources</h2>

https://docs.puppet.com/puppet/latest/reference/lang_collectors.html

https://docs.puppet.com/pe/latest/node_deactivation.html


<h2>querying puppetdb with postgres command line, psql</h2>

psql -h localhost -U puppetdb puppetdb

However to connect to puppet enterprise's puppetdb, then follow:

https://gist.github.com/tnolet/7133083

\q
\h
\l

\d
select * FROM catalog_resources
\X
\x
SELECT * FROM catalog_resourcesSELECT * FROM factsselect * from factsssselect * from reports
\q
SELECT * FROM catalog_resourcesSELECT * FROM factsselect * from factsssselect * from reports
\x
select * from facts;
\d
lsSELECT * FROM catalog_resources
\l
\c puppetdb
lsSELECT * FROM catalog_resources;
lsSELECT * FROM catalog_resources
\q
\l
\c puppetdb
\l
\d
\x
\d
SELECT * FROM certnames ;
SELECT * FROM facts ;
\d
SELECT * FROM catalog_resources ;
\x
SELECT * FROM catalog_resources ;
SELECT * FROM catalog_resources ;
\d
SELECT * FROM catalog_resources ;
\d
SELECT * FROM environments ;
SELECT * FROM catalog_resources ;
SELECT * FROM catalog_resources WHERE exported=TRUE;
SELECT * FROM catalog_resources WHERE exported=TRUE ;
SELECT * FROM catalog_resources WHERE exported=FALSE ;
\x
SELECT * FROM catalog_resources WHERE exported=FALSE ;
SELECT * FROM catalog_resources WHERE exported=FALSE ;
SELECT * FROM catalog_resources WHERE exported=TRUE ;
\q



However a better approach, as discussed in the documentation, is first create a json file with your query, e.g:


<pre>$ cat puppetdbquery.json
["and",
  ["=", "certname", "example.com"],
  ["=", "type", "file"],
  ["=", "exported", true]
]
</pre>


Then run:


<pre>
$ curl -X GET http://localhost:8080/pdb/query/v4/resources --data-urlencode query@puppetdbquery.json  --data-urlencode 'pretty=true'
</pre>


Here's a handy one liner, that lists all puppet environments currently in active use on the puppet master:


<pre>
$ curl -s -X GET http://localhost:8080/pdb/query/v4/nodes --data-urlencode 'pretty=true' | grep 'report_environment' | sort | uniq | awk '{print $NF}' | cut -d'"' -f2
</pre>