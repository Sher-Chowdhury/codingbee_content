---
ID: 195
post_title: 'Ruby &#8211; The &#8220;for&#8221; loop'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-the-for-loop
published: true
post_date: 2014-11-25 00:00:00
---
Here's an example of the for loop:

<pre>
for i in [1,2,3]
  puts i
end
</pre>

This outputs:

<pre>
PS C:\Temp\irb> ruby .\for.rb
1
2
3
PS C:\Temp\irb>
</pre>

You can also iterate through a range, e.g.:


<pre>
for i in (1..15)    # Notice that we used round bracket here. 
  puts i
end

</pre>

The ".." indicates that we are interested in a range. 

This outputs:

<pre>
PS C:\Temp\irb> ruby .\for.rb
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
PS C:\Temp\irb>
</pre>

Note, the for loop actually makes use of the "each" method behind the scenes. More about this in the next lesson.