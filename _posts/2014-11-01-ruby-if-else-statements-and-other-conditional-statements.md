---
ID: 168
post_title: 'Ruby &#8211; If-Else Statements and other conditional statements'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-if-else-statements-and-other-conditional-statements
published: true
post_date: 2014-11-01 00:00:00
---
Here's a simply if-else statement:

[ruby]
a_number = 15

if a_number &gt; 10
  puts &quot;this is a big  number&quot;
else
  puts &quot;this is a small number&quot;
end

# the above outputs: &quot;this is a big  number&quot;
[/ruby]

Another way to write the above is to capture the if-else statements output into a variable like this:


[ruby]
a_number = 15

result = if a_number &gt; 10
  &quot;this is a big  number&quot;
else
  &quot;this is a small number&quot;
end

puts result

# the above outputs: &quot;this is a big  number&quot;

[/ruby]