---
ID: 190
post_title: 'Ruby &#8211; Conditional Initialization'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-conditional-initialization
published: true
post_date: 2014-11-25 00:00:00
---
What if you want to create a new variable/object (aka initialise a varaible), but only if it doesn't already exist. This is possible using the following assingment operator:

||=    # this is called the "or equal assignment operator"

There is also a similar operator, "&&=" but this isn't used that often. 

Here's an example:

<pre>
john ||= Employee.new
</pre>

Here an object called "john" is created, but only if it doesn't already exist. 

This also works for simple variables, e.g. 

<pre>
irb(main):017:0* x = 5
=> 5
irb(main):018:0> puts x
5
=> nil
irb(main):019:0> x ||= 10
=> 5
irb(main):020:0> puts x
5
=> nil
irb(main):021:0> x = nil
=> nil
irb(main):024:0> puts x

=> nil
irb(main):025:0> x ||= 10
=> 10
irb(main):026:0> puts x
10
=> nil
irb(main):027:0>
</pre>

Note, conditional inizialation also wroks with other data types, such as strings and arrays. The only exception is that it doesn't work for setting/unsetting booleans. 

There are alternatives to using "||=", for example, by making use of the "unless" keyword:

<pre>
john = Employee.new unless john
</pre>