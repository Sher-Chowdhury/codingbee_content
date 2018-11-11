---
ID: 99
post_title: 'PowerShell &#8211;  exiting out of outerloop'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-exiting-out-of-outerloop
published: true
post_date: 2014-05-11 00:00:00
---
Special Chapter - exiting out of outerloop

when exiting out of an outer loop, you are effecting exiting out from all the inner loops. 

You do this by labeling your loops like this:

$list = 1,2,3,4,5

# here we use the ":{loops label}" syntax to give a loop a name, then refer to it when we do continue/break
:outerloop foreach ($i in $list) {
  for ($j = 0; $j -lt 5; $j++) {
    if ($j -eq 2) {
      continue outerloop  	# notice here that we are specifying which loop to apply continue to.  
    }
    Write-Host "outer loop counter = $i, inner loop counter = $j"
  }
}

For more info, see:

http://www.out-web.net/?p=785