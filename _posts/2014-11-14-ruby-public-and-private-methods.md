---
ID: 181
post_title: 'Ruby &#8211; Public and Private Methods'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-public-and-private-methods
published: true
post_date: 2014-11-14 00:00:00
---
In Ruby, all methods are public by default. 

However you can set methods to private so that they can't be accessible from outside the class. However private methods however are still inherited by child classes. 



Let's take a look at an of a class where we may want to make some methods private:

<pre>
class Employee
  
  def firstname(name)
    @firstname = name
  end
  
  def level(level)
    @level = level	
  end
  
  def salary
    @salary = 10000 * @level
	puts "salary has been calculated"
  end
  
  def net_salary
    salary                   # here we are calling the salary method. 
    puts @salary
    puts @salary - 500
  end
  
end


employee_1 = Employee.new

employee_1.firstname("John")

employee_1.level(2)

employee_1.salary     

employee_1.net_salary
</pre>




This outputs:

<pre>
PS C:\Temp\irb> ruby .\privatemethods.rb
salary has been calculated
salary has been calculated    
20000
19500
</pre>

As you can see all the methods are public by default. 

Now let's say we want to make the "salary" method private, to do this, we use the following syntax:

<pre>
private :{method-name1} 
</pre>

Here's an exmample of this syntax:


<pre>
class Employee
  
  def firstname(name)
    @firstname = name
  end
  
  def level(level)
    @level = level	
  end
  
  def salary
    @salary = 10000 * @level
	puts "salary has been calculated"
  end
  private :salary
  
  def net_salary
    salary                   
    puts @salary
    puts @salary - 500
  end
  
end


employee_1 = Employee.new

employee_1.firstname("John")

employee_1.level(2)

employee_1.salary     # this should now fail becuase salary is now a private method and can't be called from outside

employee_1.net_salary
</pre>


Now the output is:

<pre>
PS C:\Temp\irb> ruby .\privatemethods.rb
./privatemethods.rb:31:in `<main>': private method `salary' called for #<Employee:0x22cd508 @firstname="John", @level=2>
 (NoMethodError)
PS C:\Temp\irb>
</pre>


If we now comment out the "employee_1.salary" call, then the output becomes:

<pre>
PS C:\Temp\irb> ruby .\privatemethods.rb
salary has been calculated
20000
19500
PS C:\Temp\irb>
</pre>

If you also want to make the level method private too, then you can use the comma seperated syntax, then you can do:


<pre>
class Employee
  
  def firstname(name)
    @firstname = name
  end
  
  def level(level)
    @level = level	
  end
  
  def salary
    @salary = 10000 * @level
    puts "salary has been calculated"
  end
  private :salary, :level                   # use the comma seperated syntax
  
  def net_salary
    level(2)                             # I added this line so that it continues to work. 
    salary                   
    puts @salary
    puts @salary - 500
  end
  
end


employee_1 = Employee.new

employee_1.firstname("John")

# employee_1.level(2)   # is now private method so commented out

# employee_1.salary     # is now private method so commented out

employee_1.net_salary
</pre>

This will now output:

<pre>
PS C:\Temp\irb> ruby .\privatemethods.rb
salary has been calculated
20000
19500
PS C:\Temp\irb>
</pre>

So to summarise the, syntax for making multiple class's private is:

<pre>
private :{method-name1}, :{method-name1}....
</pre>

Note, if you want to make class-methods private (as opposed to instance methods), then you use the following slightly different syntax instead:

<pre>
private_class_method :{method-name1}, :{method-name1}....
</pre>



There's an alternative to using the following "private :{method-name1}, :{method-name1}...." syntax. And that is to encase the private/public parts of your code using the private/public keywords. Heres an example of this, where we have made the level and salary methods private:


<pre>
class Employee
  
  def firstname(name)
    @firstname = name
  end
  

  private                         # encasing begins here.

  def level(level)
    @level = level	
  end
  
  
  def salary
    @salary = 10000 * @level
    puts "salary has been calculated"
  end

  public                          # encasing ends here. 
  
  def net_salary
    level(2)
    salary                   
    puts @salary
    puts @salary - 500
  end
  
end


employee_1 = Employee.new

employee_1.firstname("John")

employee_1.net_salary
</pre>

Note, you can omit public if you want all the remaining methods in the class to be private. This again outputs:

<pre>
PS C:\Temp\irb> ruby .\privatemethods.rb
salary has been calculated
20000
19500
</pre>


There is also a special override that you can do to force access a method from outside the class. This is rarely used, but can be useful if you want to unit test a private method in isolation using a unit testing tool like RSpec. You use a special high level method called the "send" method to do this, here is it's syntax:


object.send(:{method_name}, intputparam1, inputparam2,....)

Here's an example:

<pre>
class Employee
  
  def firstname(name)
    @firstname = name
  end
  
  private
  def level(level)
    @level = level	
  end
  
  
  def salary
    @salary = 10000 * @level
    puts "salary has been calculated"
  end
  public
  
  def net_salary
    # level(2)
    salary                   
    puts @salary
    puts @salary - 500
  end
  
end


employee_1 = Employee.new

employee_1.firstname("John")

#employee_1.level(2)
employee_1.send(:level, 18)    # here we are using the private "level" method by force. 



# employee_1.salary   


employee_1.net_salary
</pre>

this outputs:

<pre>
PS C:\Temp\irb> ruby .\privatemethods.rb
salary has been calculated
180000
179500
PS C:\Temp\irb>
</pre>

One final thing to mention, is that child classes also automatically inherits private class just like it inherits public classes. Here's an example:


<pre>
class Employee
  
  def firstname(name)
    @firstname = name
  end
  
  private
  def level(level)
    @level = level	
  end
  
  
  def salary
    @salary = 10000 * @level
	puts "salary has been calculated"
  end
  public
  
  def net_salary
    # level(2)
	salary                   
    puts @salary
    puts @salary - 500
  end
  
end

class JuniorEmployee  < Employee
  def initialize (name,joblevel)
    firstname(name)
	level(joblevel)        # notice that private methods are still being called 
	salary                 # from inside child class, since the child class has already
                               # implicitly inherited all methods, public and private.  
	net_salary             
  end
end

employee_1 = JuniorEmployee.new("David",5)
</pre>

This outputs:

<pre>
PS C:\Temp\irb> ruby .\privatemethods.rb
salary has been calculated
salary has been calculated
50000
49500
PS C:\Temp\irb>
</pre>