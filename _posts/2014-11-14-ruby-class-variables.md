---
ID: 179
post_title: 'Ruby &#8211; Class Variables'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-class-variables
published: true
post_date: 2014-11-14 00:00:00
---
Class Variable are essentially variables that are stored by the class itself. By default class varables are not visible outside the class, i.e. they are private. So you can read/write class variables indirectly via methods (either class or instance methods will do)

<pre>
class Calculator 
  @@pi = 3.141592
  def self.pi        # note, we created a class method, so to avoid instantiating a calculator object. 
    @@pi
  end 

  def self.add(first_number,second_number)
    first_number + second_number            						 
  end
  
end

result = Calculator.add(2,5)     

puts "The sum of 2 and 5 is: #{result}"

pi_value = Calculator.pi
puts "the value of pi is #{pi_value}"

</pre>

A class variable is shared across all objects of that class. So if you design the class variable to be read-only, then everything is fine. But if you have a method that can edit the value of the class variable, then this could have an knock on effect to other objects which requires the class variable to be of the previous value. 

Because of this, class variables are not used that often. 

To make things worse, class variables are shared out across all it's child classes too!!! Here's an example of where the (parent) Calculator class get's it's "pi" class variable overwritten by the NormalCalculator class, and then overwritten again by the ScientificCalculator class:

<pre>
class Calculator 
  @@pi = 3
  def self.pi  
    @@pi
  end   
end

class NormalCalculator  < Calculator
  @@pi = 3.14
end

class ScientificCalculator  < Calculator 
  @@pi = 3.1412952
end

puts NormalCalculator.pi

</pre>

This should output "3.14", but instead it outputs:

<pre>
PS C:\Temp\irb> ruby .\class-variable.rb
3.1412952
</pre>

That's because ruby overwrites the shared class variable at the time of reading in the ruby script! It is also because class variable are shared out across all it's child classes too!!!

For this reason, class variables are not that popular. To get the NormalCalculator.pi outputting "3.14", then you need to use class instance variable instead.