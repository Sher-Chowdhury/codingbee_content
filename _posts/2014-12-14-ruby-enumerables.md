---
ID: 223
post_title: 'Ruby &#8211; Enumerables'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-enumerables
published: true
post_date: 2014-12-14 00:00:00
---
In ruby "Enumerables" is actually name of module. A module is essentially a collection of constants and methods. 

The Enumerables module contains a collection of methods that are specifically designed to iterate through a collection of objects (e.g. elements in an array), and run some ruby code against each object.    

for this reason, the enumerables methods comes built into the array class. 

One commonly used enumerable is the "map", this method lets you generate a new a new array from an existing class, using the block-form syntax:

<pre>
irb(main):241:0* arr = [11,12,13,14]
=> [11, 12, 13, 14]
irb(main):242:0> arr.map {|value| value - 2 }
=> [9, 10, 11, 12]
</pre>


There's also a "<a href="http://ruby-doc.org/core-2.1.5/Enumerable.html#method-i-reduce">reduce</a>" that you can use to sum up the numbers in an array:


<pre>
irb(main):251:0> [1,2,3].reduce(0) {|sum, v| sum + v}
=> 6
</pre>

The zero parameter for the "reduce" method is the initial value that the "sum" variable takes, e.g.:

<pre>
irb(main):252:0> [1,2,3].reduce(4) {|sum, v| sum + v}
=> 10
</pre>

The enumerable method also has a "select" and "sort" methods:

<pre>
irb(main):276:0> [100,12,43,5,58,150].select {|n| n.even?}
=> [100, 12, 58, 150]
irb(main):277:0> [100,12,43,5,58,150].sort.select {|n| n.even?}
=> [12, 58, 100, 150]
irb(main):278:0>
</pre> 

As you can see the "select" method lets you intelligently select a subset of an array, based on elements meeting a certain criteria. E.g. if "n.even?" is true, then element get's selected. 


See also:
http://ruby-doc.org/core-2.1.5/Enumerable.html