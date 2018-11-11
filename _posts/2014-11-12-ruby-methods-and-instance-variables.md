---
ID: 174
post_title: 'Ruby &#8211; Methods and Instance Variables'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-methods-and-instance-variables
published: true
post_date: 2014-11-12 00:00:00
---
In ruby you define a method with the <strong>def </strong>keyword. In the following example I've defined a method called "salary" which takes an input parameter called "job_title" : 

<pre>
class Employee
  def salary(job_title)
  end
end
</pre>


Next we want to store the input parameter as data that is particular to a given a instance of this class. To do this we create an "instance variable". Instance variable are variables that stores data for a particular instance of a class. You can create an instance variable by prefixing the variable name with an "@", e.g.:

 
<pre>
class Employee
  def salary(job_title)
    @job_title = job_title
  end
end

# Now let's try out this class:
john = Employee.new
john.salary("Level 2 Manager")
puts john.inspect                  # a shorthand for this line is to simply type "p ship"

</pre>

In this case I have named this ruby script file as employeeclass.rb, so from the powershell command line you do:

<pre>
> ruby c:\path\to\file.rb
</pre>

Hence the above script outputs:

<pre>
PS C:\Temp\irb> ruby .\employeeclass.rb
#<Employee:0x1f2f000 @job_title="Level 2 Manager">    # this is the output from the inspect method.
PS C:\Temp\irb>
</pre>

Also the following will not work:


<pre>
class Employee
  def salary(job_title)
    @job_title = job_title
  end
end

john = Employee.new
john.salary("Level 2 Manager")

puts john.job_title      # This line will fill due to OOP encapsulation rules. 

</pre>

The same OOP principes of c#, also applies here. 

The OOP law of encapsulation applies here, this means that you can't (and not supposed) to get/set instance variables directly. i.e. instance variables are supposed to be private, and internal to the class. The only elements that are public in a class, are the methods. 

This means that the only way to set/get instance variables, is indirectly through methods, e.g.:


<pre>
class Employee
  def salary(job_title)
    @job_title = job_title
  end

  def job_title      # here we created a new method just to get the instance variable. 
    @job_title
  end
end

john = Employee.new
john.salary("Level 2 Manager")

puts john.job_title      # This line will fill due to OOP encapsulation rules. 

</pre>

This outputs:

<pre>
PS C:\Temp\irb> ruby .\employeeclass.rb
Level 2 Manager
</pre>