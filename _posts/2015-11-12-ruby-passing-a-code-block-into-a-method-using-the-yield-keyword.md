---
ID: 482
post_title: 'Ruby &#8211; Passing a code block into a method using the &#8220;yield&#8221; keyword'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-passing-a-code-block-into-a-method-using-the-yield-keyword
published: true
post_date: 2015-11-12 00:00:00
---
<pre>
$ cat testscript.rb
#!/usr/bin/env ruby

def normal_welcome
   puts "about to execute:"
   yield
   puts "finished executing"
end

normal_welcome do
    puts 1 + 1
    puts 2 + 2
    puts 3 + 3
    puts 4 + 4
end
</pre>

The above outputs: 

<pre>
$ ./testscript.rb
about to execute:
2
4
6
8
finished executing
</pre>