---
ID: 186
post_title: 'Ruby &#8211; If-else statements'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-if-else-statements
published: true
post_date: 2014-11-21 00:00:00
---
Here's an example of a simple if-statement:

<pre>
a_number = 5

if a_number < 10
  puts "#{a_number} is less than 10"
end  
</pre>

This outputs:

<pre>
PS C:\temp\irb> .\if-else.rb
PS C:\temp\irb> ruby .\if-else.rb
5 is less than 10
PS C:\temp\irb>
</pre>

Now here's how an if-else statement looks like:

<pre>
a_number = 13

if a_number < 10
  puts "#{a_number} is less than 10"
else  
  puts "#{a_number} is greater than 10"
end  
</pre>

This outputs:

<pre>
PS C:\temp\irb> ruby .\if-else.rb
13 is greater than 10
PS C:\temp\irb>
</pre>

Now here's an example of an elsif statment (note, it is spelt "elsif" and not "elseif"):

<pre>
a_number = 13

if a_number < 10
  puts "#{a_number} is less than 10"
elsif  a_number == 13
  puts "#{a_number} is an unlucky number"
else
  puts "#{a_number} is greater than 10"
end  
</pre>

This outputs:

<pre>
PS C:\temp\irb> ruby .\if-else.rb
13 is an unlucky number
PS C:\temp\irb>
</pre>

In ruby, only the keywords "nil" and "false" evaluates to be false. Everything else (including empty strings/arrays and "0") evaluates to be true. Here are some examples:

<pre>
class Human
  attr_reader :name
 
  def initialize(surname) 
    @name = surname
  end 

  def greeting 
    puts "hello everybody"
	false
  end
  
  def hungry 
    puts "I am hungry"
	true
  end
end

john = Human.new(nil)


if john.name 
  puts "john has a surname which is '#{john.name}'"
else 
  puts "john has no surname"  
end  


if john.greeting 
  puts "this is a geniune greeting"
else 
  puts "this is a sarcastic greeting"
end  
 
if john.hungry
  puts "John is genuinely hungry"
else 
  puts "John is lying and isn't that hungry"
end  

</pre>

This outputs:

<pre>
PS C:\temp\irb> ruby .\if-else.rb
john has no surname
hello everybody
this is a sarcastic greeting
I am hungry
John is genuinely hungry
PS C:\temp\irb>
</pre>


One of the things commonly done, is to use an if-else statment to determine a value for a variable. Here is an example where we are trying to determine the value of the variable called "message":


<pre>
a_number = 13

if a_number < 10
  puts "#{a_number} is less than 10"
  message = "Hence this is a small number."
else  
  puts "#{a_number} is greater than 10"
  message = "Hence this is a big number."
end  

puts message
</pre>

This outputs:

<pre>
PS C:\temp\irb> ruby .\if-else.rb
13 is greater than 10
Hence this is a big number.
PS C:\temp\irb>
</pre>


You can rewrite this in a way to show to show that you are capturing the output of the if statement, like this:

<pre>
a_number = 13

message = if a_number < 10
  puts "#{a_number} is less than 10"
  "Hence this is a small number."
else  
  puts "#{a_number} is greater than 10"
  "Hence this is a big number."
end  

puts message
</pre>

If you have a really simple if-else statement, e.g.:

<pre>
age = 15

price = if age <= 16   
  "half price"
else  
  "full price"
end  

puts price            # outputs: "half price"
</pre>

Then it might be worth writing this if-else statement on a single line without sacrificing readability:

<pre>
age = 15

price = if age <= 16 then "half price" else "full price" end  

puts price            # outputs: "half price"
</pre>

Notice the inclusion of the "then" keyword here. You only use the "then" keyword when you squeeze an if-statement into one line, as we have done above. 

If you just have a very simply if statement, and no else statement, then you can have even shorter single line if statements, here's an example:


<pre>
def success_message
  puts "this is a success"
end

def result 
  true
end

success_message if result      # outputs "this is a success"
</pre>