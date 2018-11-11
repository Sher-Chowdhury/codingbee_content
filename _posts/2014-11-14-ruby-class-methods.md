---
ID: 178
post_title: 'Ruby &#8211; Class Methods'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-class-methods
published: true
post_date: 2014-11-14 00:00:00
---
A class method is essentially the c# equivalent of a "static method". A good example would be a "calculator" class, where you have a "class method" called "add" which adds 2 numbers together. 

In this situation, it makes no sense to create a "calculator" object and then use the "add" method. Instead we would want to use the "add" method directly.  

To define a class-method, we once again make use of the "self" keyword:

<pre>
class Calculator 
  def self.add(first_number,second_number)
    first_number + second_number             # remember the output is automatically returned. 
                                             # You don't need to explicitly state a return statement
											 
  end
  
end

result = Calculator.add(2,5)     # here we are just drilling down to the class 
                                 # that has the "add" method, similar to c#.

puts "The sum of 2 and 5 is: #{result}"
</pre>