---
ID: 226
post_title: 'Ruby &#8211; Parrallel Assignment'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-parrallel-assignment
published: true
post_date: 2014-12-14 00:00:00
---
This is to do with defining multiple variables on a single line:

<pre>
irb(main):001:0> a,b = 7,16
=> [7, 16]
irb(main):002:0> a
=> 7
irb(main):003:0> b
=> 16
irb(main):004:0>
</pre>

A similar approach can also be used to create an array:

<pre>
irb(main):004:0> arr = 1,5,10,15,20
=> [1, 5, 10, 15, 20]
irb(main):005:0> arr[2]
=> 10
irb(main):006:0>
</pre>


You can also create a bunch of variables by taking values from an array:

<pre>
irb(main):001:0> a,b,c,d = ["alpha","beta","charlie","delta"]
=> ["alpha", "beta", "charlie", "delta"]
irb(main):002:0> a
=> "alpha"
irb(main):003:0> b
=> "beta"
irb(main):004:0> c
=> "charlie"
irb(main):005:0> d
=> "delta"
</pre>

If you had fewer varable names than elements in an array, then the remaining array values are ignored. 


Alternatively you can use the splat operator to capture the remaining elements into an array:

<pre>
irb(main):030:0* a, *b = ["alpha","beta","charlie","delta"]
=> ["alpha", "beta", "charlie", "delta"]
irb(main):031:0> a
=> "alpha"
irb(main):032:0> b
=> ["beta", "charlie", "delta"]
irb(main):033:0>
</pre>

Splat acts in a greedy way, taking as much elements is it logically can:

<pre>
irb(main):034:0> a, *b, d = ["alpha","beta","charlie","delta"]
=> ["alpha", "beta", "charlie", "delta"]
irb(main):035:0> a
=> "alpha"
irb(main):036:0> b
=> ["beta", "charlie"]
irb(main):037:0> d
=> "delta"
irb(main):038:0>
</pre>

This could be used to quickly capture the first and last values into an array. 

you can use splatting on the right side as well:

<pre>
irb(main):043:0> a,b,c = 1..3
=> 1..3
irb(main):044:0> a
=> 1..3
irb(main):045:0> b    # notice that this didn't work, we have nil. 
=> nil
irb(main):046:0> c
=> nil
irb(main):047:0> a,b,c = *1..3
=> [1, 2, 3]
irb(main):048:0> a
=> 1
irb(main):049:0> b        # success
=> 2
irb(main):050:0> c        # more success. 
=> 3
irb(main):051:0>
</pre>