---
ID: 211
post_title: 'Ruby &#8211; String Operators'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-string-operators
published: true
post_date: 2014-12-02 00:00:00
---
<pre>
PS C:\Temp\irb> irb
irb(main):001:0> "Hello world"[1]   # Returns a single character from specified position
=> "e"
irb(main):002:0> "Hello world"[2,3]   # Returns the next 3 characters from position 2
=> "llo"
irb(main):003:0> "Hello world"["ell"]   # Searches for a match
=> "ell"
irb(main):004:0> "Hello world"["elx"]   # Searches for a match
=> nil
irb(main):005:0> message = "Hello CodingBee"
=> "Hello CodingBee"
irb(main):006:0> puts message
Hello CodingBee
=> nil
irb(main):007:0> message["Bee"] = "Duck"  # This does a find and replace, and makes it a permenant change to string. But only changes the firstInstance of a match. 
=> "Duck"
irb(main):008:0> puts message
Hello CodingDuck                
=> nil
irb(main):001:0> "hello! " *3
=> "hello! hello! hello! "
irb(main):002:0> "abc" + "def"
=> "abcdef"
irb(main):003:0>


</pre>