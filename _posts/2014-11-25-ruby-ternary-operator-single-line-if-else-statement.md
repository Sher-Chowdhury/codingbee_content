---
ID: 189
post_title: 'Ruby &#8211; Ternary Operator (single line If-Else statement)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-ternary-operator-single-line-if-else-statement
published: true
post_date: 2014-11-25 00:00:00
---
Previously we saw how you could fit an if-else statement into a single line with the help of the "then" keyword. However there is another commonly used syntax that you could use instead, which is known as the <a href="http://en.wikipedia.org/wiki/Ternary_operation">ternary operator (:?)</a>. Ternary Operators are also used in other languages such as java and c#. The ternary operator syntax is essentially as follows:

<pre>
{condition} ? {if-code-block} : {else-code-block}
</pre>

Here's an example:

<pre>
def success_message
  puts "this is a success"
end

def fail_message
  puts "this is a failure"
end

def result 
  true
end

result ? success_message : fail_message    # this outputs "this is a success"

def result 
  false
end

result ? success_message : fail_message    # this outputs "this is a failure"
</pre>