---
ID: 197
post_title: 'Ruby &#8211; Creating an infinite loop'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-creating-an-infinite-loop
published: true
post_date: 2014-11-26 00:00:00
---
You can also create an infinite loop using the "loop" key word and the "do...end" block:

<pre>
loop do
  puts Time.now
  sleep(5)        # 5 second sleep 
end
</pre>

This will output something like:

<pre>
irb(main):001:0> exit
PS C:\Temp\irb> ruby .\loop.rb
2014-11-26 10:14:08 +0000
2014-11-26 10:14:13 +0000
2014-11-26 10:14:18 +0000
2014-11-26 10:14:23 +0000
.
.
.
....etc
</pre>

You can also achieve the same outcome by setting up a while-true loop.