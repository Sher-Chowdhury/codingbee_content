---
ID: 194
post_title: 'Ruby &#8211; The &#8220;until&#8221; loop'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-the-until-loop
published: true
post_date: 2014-11-25 00:00:00
---
The until-loop is basically the reverse of the until-loop. The syntax of the until loop, here's an example:

<pre>
x = 0
until x == 10
  puts x
  x+=1
end
</pre>
 
This outputs:

<pre>
PS C:\Temp\irb> ruby .\until.rb
0
1
2
3
4
5
6
7
8
9
PS C:\Temp\irb>
</pre>

As before, you can squeeze this until-loop into a single line, like this:

<pre>
x = 0
until x == 10 do puts x ; x+=1 end
</pre>


Finally we have the do-until loop, which is equivalent to the do-while loop that we saw earlier:

<pre>
x = 11
begin
  puts x
  x+=1
end until x >= 10
</pre>

This outputs:

<pre>
PS C:\Temp\irb> ruby .\until.rb
11
PS C:\Temp\irb>
</pre>