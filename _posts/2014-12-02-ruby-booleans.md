---
ID: 206
post_title: 'Ruby &#8211; Booleans'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-booleans
published: true
post_date: 2014-12-02 00:00:00
---
In Ruby, there is actually no class called "Boolean" that instantiates true/false values. Instead, we have the following 2 classes:

<ul>
	<li>TrueClass - which instantiates "true" values</li>
	<li>FalseClass - which instantiates "false" values</li>
</ul>

Both of these classes are dervived from the "Object" class. This means that these true/false objects have access to all the methods that are defined in Object, e.g.:


<pre>
puts true.class
puts false.class
puts nil.class


puts true.class.superclass
puts false.class.superclass
puts nil.class.superclass


puts true.to_s      # outputs the string "true"
puts false.nil?     # outputs false, since false itself is an 
                    # object that has instantiated from "FalseClass" class. 
</pre>

This outputs:

<pre>
PS C:\Temp\irb> ruby .\boolean.rb
TrueClass
FalseClass
NilClass
Object
Object
Object
true
false
PS C:\Temp\irb>

</pre>