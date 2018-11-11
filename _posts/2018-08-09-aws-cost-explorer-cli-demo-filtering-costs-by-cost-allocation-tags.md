---
ID: 3796
post_title: 'AWS Cost Explorer CLI demo &#8211; filtering costs by cost allocation tags'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-cost-explorer-cli-demo-filtering-costs-by-cost-allocation-tags
published: true
post_date: 2018-08-09 16:16:24
---
Here's a quick example:

<pre>
$ cat filter.json
{
  "Tags": {
    "Key": "role",
    "Values": ["webserver"]
  }
}

$ aws ce get-cost-and-usage --time-period Start=$(date +"%Y-%m-%d" --date="-24 hours"),End=$(date +"%Y-%m-%d") --granularity=DAILY  --metrics BlendedCost --filter file://filter.json

RESULTSBYTIME	True
TIMEPERIOD	2018-08-09	2018-08-08
BLENDEDCOST	22.30	USD
</pre>


References: 

http://blog.xi-group.com/2015/01/small-tip-how-to-use-aws-cli-filter-parameter/

https://medium.com/@nachomillangarcia/prometheus-alerts-for-aws-daily-costs-3374137d1371