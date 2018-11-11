---
ID: 2429
post_title: Register external service to consul
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/uncategorized/register-external-service-to-consul
published: true
post_date: 2018-01-18 09:46:29
---
https://www.consul.io/docs/guides/external.html


https://aws.amazon.com/blogs/aws/keeping-time-with-amazon-time-sync-service/


https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/set-time.html#configure-amazon-time-service



<pre>
$ curl -X PUT -d '{
"Node": "aws-ntp",
"Address": "169.254.169.123",
"Service": {
"Service": "ntp"
}
}' http://localhost:8500/v1/catalog/register
</pre>

nslookup of ntp.service.{{domain}} should now work. 

you can then populate /etc/ntp.conf with 'ntp.service.{{domain}}' using consul templates. 



In case you want to deregister this service from consul, run:


<pre>
$ curl --request PUT --data @deregister.json http://localhost:8500/v1/catalog/deregister
</pre>

where: 

<pre>
$ cat deregister.json
{
"Node": "aws-ntp",
"Address": "169.254.169.123",
"Service": {
"Service": "ntp"
}
}
</pre>