---
ID: 192
post_title: 'Ruby &#8211; The case statement'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-the-case-statement
published: true
post_date: 2014-11-25 00:00:00
---
Here is how a case statement looks like in ruby:

<pre>
def salutations (string_var)
  case string_var
  when "goodbye"
    puts "bye for now"
  when "handshake"
    puts "let shake hands"
  when "Hello"
    puts "Hello world"
  else
    puts "The message is: #{string_var}"
  end  
end

salutations "handshake"
salutations "hi"
</pre>

This outputs:

<pre>
PS C:\Temp\irb> ruby .\CaseStatement.rb
let shake hands
The message is: hi
PS C:\Temp\irb>
</pre>

Notice that the "else" key word is used here in the context of a catchall system. The else keyword is optional and can be left out, in which case if no matches are made then the case statement won't output anything. 

You can also capture the output of a case statement into a variable, here's an example:


<pre>

def salutations (string_var)
  case string_var
  when "goodbye"
    puts "bye for now"
	return "Goodbye to you too"
  when "handshake"
    puts "let shake hands"
	return "I will shake hands with you to"
  when "Hello"
    puts "Hello world"
	return "Hello to you too"
  else
    puts "The message is: #{string_var}"
	return "I don't know how to respond to that"
  end  
end

response =  salutations "handshake"
puts response

response = salutations "hi"
puts response
</pre>

Here's another way to do it:

<pre>
weight = 45


response =  case
  when weight == 65
	 "You are average weight"
  when weight > 65
	 "You are overweight"
  when weight < 65
     "you are under weight"
  else
	"not sure whether you are over or under weight"
  end  

puts response
</pre>