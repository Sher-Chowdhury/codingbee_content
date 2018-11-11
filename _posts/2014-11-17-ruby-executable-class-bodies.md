---
ID: 183
post_title: 'Ruby &#8211; Executable Class Bodies'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-executable-class-bodies
published: true
post_date: 2014-11-17 00:00:00
---
It is possible for a class to be used in the style of a method. 

Here's an example:

<pre>
total_sum = class SimpleSum
  sum_of_two_numbers = 7 + 3
  sum_of_two_numbers
end

puts total_sum
puts SimpleSum.superclass
</pre>

This outputs:

<pre>
PS C:\Temp\irb> ruby .\simplesum.rb
10
Object
PS C:\Temp\irb>
</pre>

It is very unlikely that you will need to classes in this way in real life. But it is possible if you need to.