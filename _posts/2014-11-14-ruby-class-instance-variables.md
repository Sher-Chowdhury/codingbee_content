---
ID: 180
post_title: 'Ruby &#8211; Class Instance Variables'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-class-instance-variables
published: true
post_date: 2014-11-14 00:00:00
---
In the last lesson we saw how we only have one copy of a class variable that is shared across a class and all it's child class. If you want to have a class variable that is only shared across it's and not it's child classes, then you need to use something called "Class instance Variables" instead. A Class Instance Variable is essentially an instance variable that is set at the class level rather than the method level. 


Previously we explained that instance variables are used to store information of a particular instance of a class. Also due to encapsulation, you can't get/set instance variable's directly, instead you have to set/get instance variables indirectly through the use of methods, here's an example:

<pre>
class Employee
  def set_job_title(job_title)
    @job_title = job_title
  end
  
  def get_job_title
    @job_title
  end  
end

john = Employee.new
john.set_job_title("Level 2 Manager")

puts john.get_job_title
</pre>

This outputs:

<pre>
PS C:\Temp\irb> ruby .\instance-variable.rb
Level 2 Manager
</pre>


You can rewrite the above in shorthand form with the help of the accessor notation:

<pre>
class Employee
  attr_accessor :job_title
end

john = Employee.new
john.job_title = "Level 2 Manager"

puts john.job_title
</pre>

Again you use the the get/set methods, but they are all hidden away in the backend. 

This again outputs:

<pre>
PS C:\Temp\irb> ruby .\instance-variable.rb
Level 2 Manager
</pre>

So up to now, we have defined/edited instance variables from inside the method blocks. 

However you can also define instance variables directly under the class level. These class level, instance variables are refered to as "Class Instance Variables".

Here's the calculator example, from the previous lesson, but this time using class instance variables rather than class variables:


<pre>
class Calculator 
  @pi = 3
  def self.pi  
    @pi
  end   
end

class NormalCalculator  < Calculator
  @pi = 3.14
end

class ScientificCalculator  < Calculator 
  @pi = 3.1412952
end

puts NormalCalculator.pi
</pre>
All that has been done here is tha the every class variable reference has been replaced by a class instance reference, by simply removing the extra "@" symbol. The output is now:

<pre>
PS C:\Temp\irb> ruby .\class-instance-variable.rb
3.14
PS C:\Temp\irb>
</pre>