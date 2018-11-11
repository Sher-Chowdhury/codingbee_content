---
ID: 198
post_title: 'Ruby &#8211; Looping through numbers tips and tricks'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-looping-through-numbers-tips-and-tricks
published: true
post_date: 2014-11-26 00:00:00
---
Here we have a number of handy integer (fixnum) class methods:

 
<pre>
irb(main):012:0> 5.upto(10) {|number| puts number}
5
6
7
8
9
10
=> 5
irb(main):013:0> 5.downto(1) {|number| puts number}
5
4
3
2
1
=> 5
irb(main):014:0> 4.times { puts "hello"}
hello
hello
hello
hello
=> 4
irb(main):015:0> 2.step(15,3) {|i| puts i}
2
5
8
11
14
=> 2
irb(main):016:0>
</pre>