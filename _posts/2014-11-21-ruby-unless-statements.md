---
ID: 187
post_title: 'Ruby &#8211; &#8220;unless&#8221; statements'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-unless-statements
published: true
post_date: 2014-11-21 00:00:00
---
Sometimes you might want to create an "if not" statement. You can use the if-statement to do this, but another option is using the "unless" statement instead. That's because the unless-statement can be a little easier to read.

Here's an example:

<pre>
weight = 90

unless weight < 85
  puts "you are overweight, so you need to go on a diet and have plenty of exercise"
end
</pre>

You should read this statement, as "do whatever is inside the block, unless weight is less than 85".

This will output:

<pre>
PS C:\temp\irb> ruby .\unless.rb
you are overweight, so you need to go on a diet and have plenty of exercise
PS C:\temp\irb>
</pre>

You can also add an "else" clause into the unless statement, but it is best to avoid this since it can make the code a little difficult to follow. 

With if-statements we saw how you could condense an if statement into a single line. We can do the same with the unless statement too, here's an example:


<pre>
def fail_message
  puts "this is a failure"
end

def result 
  false
end

fail_message unless result  # this outputs: "this is a failure"
</pre>