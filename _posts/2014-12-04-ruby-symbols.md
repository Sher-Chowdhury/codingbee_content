---
ID: 216
post_title: 'Ruby &#8211; Symbols'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-symbols
published: true
post_date: 2014-12-04 00:00:00
---
Sybmols are essentially a special kind of object. They are denoted by a colon prefixing the object's name, e.g.:

:{object-name}

You can think of them as a mix between a unique constant and a string, i.e. a constant/string hybrid.  

You can think of a symbol, where the name of the variable itself is what we're interested in, and not what value it hold's, hence by default a symbol's value defaults to holding the string of it's own name: 

<pre>
irb(main):124:0> :var1
=> :var1
irb(main):125:0> puts :var1
var1
=> nil
irb(main):126:0> :var1 = "test"   # in fact you can't do this, it will give an error message.
</pre>


It also means that you don't need to set a value of a symbol, you just start using it when you need it.  


We've already come across symbols, as a way to define attributes for a class.  


<pre>
# Here we are defining a class called "Employee"
class Employee
  attr_accessor :age     # Notice that we used the special "attr_accessor"  
end

john = Employee.new
john.age = "John"      # here we are setting the instance variable
puts employee1.age           # here we are getting the instance variable
</pre>

What happens here is that attr_accessor has to generate a "get" and "set" method, along with an instance variable. In order to do this, attr_accessor needs to first figure out what to name these methods, and that's where the ":age" symbol comes in. attr_accessor essentially uses ":age" as a template to generate the method names and instance name, hence behind the scenes attr_accessor, get's expanded into something like this:

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
john.set_age(25)      # Notice that the syntax changed a bit, i.e. we are no longer using "="
puts john.get_age     # also notice that we now have to use "set_age" and "get_age" methods.     
</pre>
     
Notice that we also had to change the syntax a bit, using the new methods set_age and get_age. we also have to use the "john.set_age(25)" rather than using the "=". However you can still go back to using the "=" sign by using the following syntactic approach:


<pre>
class Employee
  def set_age=(years)      # notice the "=" syntax. 
    @age = years
  end

  def get_age
    @age
  end

end

john = Employee.new
john.set_age = 25      # here we can use the "=" again
puts john.get_age         
</pre>

This is getting really complicated, that's why as you can see, with symbols (and in this case with conjunction with attr_accessor), the code looks and reads a lot simpler. Symbols make life easier elsewhere too. 


Each symbol is an instance of the <a href="http://www.ruby-doc.org/core-2.1.5/Symbol.html">Symbol</a> class. 

symbol literal:

:"abcd"
:"5"


name = ""


http://www.randomhacks.net/2007/01/20/13-ways-of-looking-at-a-ruby-symbol

http://programmers.stackexchange.com/questions/24460/what-is-a-symbol-in-ruby