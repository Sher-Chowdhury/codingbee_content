---
ID: 208
post_title: 'Ruby &#8211; Strings'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-strings
published: true
post_date: 2014-12-02 00:00:00
---




In Ruby, you can find the encoding used like this:

<pre>
irb(main):005:0* puts "abc".encoding
UTF-8
=> nil
irb(main):006:0>
</pre>

You can change this by writing the follow special comment:

<pre>
irb(main):005:0* puts "abc".encoding
UTF-8
=> nil
irb(main):006:0>
irb(main):002:0> # encoding: us-ascii
irb(main):003:0* puts "abc".encoding
US-ASCII
=> nil
</pre>

In a ruby script, this encoding command has to appear on the first line of the file, or the second line, if the first line is a she-bang line, e.g.:

<pre>
#!/usr/bin/ruby
# encoding: UTF-8
.
.
.
</pre>