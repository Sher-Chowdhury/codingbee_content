---
ID: 207
post_title: 'Ruby &#8211; Numbers'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-numbers
published: true
post_date: 2014-12-02 00:00:00
---
Ruby has 2 classes from which integers can be instantiated from:

<ul>
	<li>Fixnum</li>
	<li>Bignum</li>
</ul>

Both of these classes are derived from the class called "Integer". I.e. they are child class of Integer.

The range of number that Fixnum supports depends on your machine's setting and version of Ruby installed. whereas Bignum is limited by the amount of ram you have on your machine.  Here is the boundary on my machine:

<pre>
puts 1070000000.class
puts 1080000000.class
</pre>  

Outputs:

<pre>
PS C:\Temp\irb> ruby .\numbers.rb
Fixnum
Bignum
PS C:\Temp\irb>
</pre>

Ruby handles the conversions of these two types automatically. 

Floating numbers, on the otherhand are instantiated from the "Float" class, e.g.:

<pre>
puts 10.5321345
puts 10.5e3

puts 10.5321345.class
puts 10.5e3.class
</pre>

This outputs:

<pre>
PS C:\Temp\irb> ruby .\numbers.rb
10.5321345
10500.0
Float
Float
PS C:\Temp\irb>
</pre>

Note: you need to have one digit before a the decimal point, and one after. 

Note, objects that hold numbers, are set to read-only. Becuase of this, increment/decrement notations like i++ and i-- don't exist in ruby. Instead the closest thing to this that you have is:

<pre>
a = 1
puts a
a = a + 1
puts a
a += 1    # shorthand way of writing a = a +1
puts a
# a++     # this won't work and will output error message. 

</pre>

This outputs:

<pre>
PS C:\Temp\irb> ruby .\numbers.rb
1
2
3
PS C:\Temp\irb>

</pre>