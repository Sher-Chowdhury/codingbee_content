---
ID: 165
post_title: 'Ruby &#8211; Storing data inside an Object (aka Instance Variables)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-storing-data-inside-an-object-aka-instance-variables
published: true
post_date: 2014-10-30 00:00:00
---
In OOP, encapsulation is really important in order have class be properly self contained. For that reason, when you create a new instance of a class, then any variables that are stored inside it are private, and can't be accessed directly. 

The only way thing that is public, are the methods defined in the class. Hence an indirect way to get/set an instance variable is by creating a pair of methods for each variable. One method (aka set method) is used to set the variable (hence requires an input parameter, but doesn't return a value), and another method (aka get method) is used to retrieve the variable's value (hence this method has return value, but doesn't required an input parameter).

This <a href="http://codingbee.net/tutorials/c/c-storing-data-inside-objects-using-properties/">ecapsulation concept works in c# too</a>. 

Instance Variables are defined using the "@" sybmol, e.g.

<pre>
@test_var = "hello world"
</pre>

We have three possible scenarios, when setting/getting objects:
- you want to set only, and not get (in which case you only need to define a set-method, aka it is write only variable)
- you want to get only and not set (in which case you need to define a get-method, aka it s a read-only variable)
- you want to both set and get (in which case you need to define both a get-method and set-method, aka it is a variable with read-write priveleges)

For each variable, it can get a bit tedious constantly writing respective get and set methods. This will also add a lot more clutter in your code. Fortunately ruby provides a special shorthand notation that greatly simplify this, here is an example

<pre>
# Here we are defining a class called "Employee"
class Employee
  attr_accessor :name     # Notice that we used the special "attr_accessor"  
end

employee1 = Employee.new
employee1.name = "John"      # here we are setting the instance variable
puts employee1.name           # here we are getting the instance variable
</pre>

Note: here we are using a <a href="http://codingbee.net/tutorials/ruby/ruby-symbols/" title="Ruby - Symbols">symbol</a> called ":name". Symbols starts with a colon. We will cover symbols a bit more further down. 

As you can see the "attr_accessor" gives both get and set privileges. If you just want to set a value then use "attr_writer" instead. Also if you just want to read a value then just use "attr_reader". 

these accessors are just special notation that simplifies the code writing, the essentially just create the necessary get/set methods behind the scenes. 


You can also define multiple instance variables like this:


<pre>
class Employee
  attr_accessor :first_name, :surname, :job_title
end


employee1 = Employee.new
employee1.first_name = "John"              # essentially here we are calling a 
employee1.surname = "Smith"                # bunch of hidden set methods. 
employee1.job_title = "Level 2 Manager" 

puts employee1.first_name        # essentially here we are calling a 
puts employee1.surname           # bunch of hidden get methods. 
puts employee1.job_title 
</pre> 

This outputs:

<pre>
PS C:\Temp\irb> ruby .\employeeclass.rb
John
Smith
Level 2 Manager
PS C:\Temp\irb>
</pre>
 
Note, attr_accessor, reader_accessor, and writer_accessor, are used in conjunction with instance variables only. They can't be used for other variable types such as local or global variables. 

Also remember that it is possible to have an instance variable and local variable of the same name in the same class. This can create a bit of confusion when we call a variable. The way around this is to use the "self" keyword when we are calling an instance variable:


<pre>
class Employee
  attr_accessor :first_name, :surname, :job_title
  

  def DummyMethod
    first_name = "Jane"                
    puts first_name               # this outputs the local variable. 
    puts self.first_name          # this outputs the instance variable. 
  end
end


employee1 = Employee.new
employee1.first_name = "John"
employee1.surname = "Smith"
employee1.job_title = "Level 2 Manager" 

employee1.DummyMethod
</pre>

Behind the scenes, the "self" keyword is calling the accessor method. 

This outputs:

<pre>
PS C:\Temp\irb> ruby employeeclass.rb
Jane
John
</pre>


Finally let's see what happens behind the scenes when we are using <a href="http://codingbee.net/tutorials/ruby/ruby-symbols/" title="Ruby - Symbols">symbols </a>and attr_accessor.  Let's say we have:

<pre>


class Employee
  attr_accessor :age     # Notice that we used the special "attr_accessor"  
end

john = Employee.new
john.age = 25      # here we are setting the instance variable
puts john.age           # here we are getting the instance variable
</pre>

In the background, i.e. behind the scenes, this get's expanded to:

<pre>
class Employee
  def set_age (years)
    @age = years
  end

  def get_age
    @age
  end

end

john = Employee.new
john.set_age(25)      # here we are setting the instance variable
puts john.get_age           # here we are getting the instance variable

</pre>

As you can see using symbols (and in this instance, accessors) can really make things shorter and simpler syntactly.