---
ID: 17
post_title: 'c# &#8211; Instantiate an object and set it&#8217;s Properties simultaneously (using the Constructors approach)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/c/c-instantiate-an-object-and-set-its-properties-simultaneously-using-the-constructors-approach
published: true
post_date: 2014-05-10 00:00:00
---
<h3>2nd Approach - Using the "Constructor"</h3>
Constructor is a special type of method that is present in all classes, even if you don't explicitly define it in the class itself.

Constructors are used for creating an instance of the object.... this basically means that whenever you declare a new instance of a class, e.g.:
<pre>        Employee Dave = new Employee()</pre>
Then the constructor get's executed, and in the above case, the only thing it does is set a side a memory container, waiting to be filled in. You can identify a constructor in a class by the fact that the constructor's name must be the same as the class's name itself. Hence in the case of the the "Employee" class, we didn't explicitly declare the Employee constructor, so the following constructor method is implicitly included:

public Employee()
{

}

Note: not only is the constructor method named after the class name, but it also never has a return value....not even "void".

The above function is empty.....and comes into play when you run:
<pre>        Employee Dave = new Employee()</pre>
This line says:

"Create an object called 'Dave' that belongs to the 'employee' class, and do this by running the 'Employee()' (constructor) method."

Since the Employee() hasn't been changed from the default, it has only ended up creating an empty memory container.

However we can utilize the constructor to set all the properties as well, as shown here:

[csharp]
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

class Employee
{
    public string Firstname { get; set; }
    public int Age { get; set; }
    public double Salary { get; set; }

    public Employee(string firstname, int age, double salary)
    {

        Firstname = firstname;
        Age = age;
        Salary = salary;
    }

}

class Program
{
    static void Main()
    {
	Employee Dave = new Employee(&amp;quot;David&amp;quot;, 35, 35235.23);
        Console.WriteLine(&amp;quot;{0} is {1} years old and earns  £{2} per year.&amp;quot;, Dave.Firstname, Dave.Age.ToString(), Dave.Salary.ToString());
    }
}

[/csharp]

As you can see, we have now modified the constructor method, to accept 3 input parameters. As a result you can no longer use the following statement:
<pre>        Employee Dave = new Employee()</pre>
That's because due to the concept of method-overloading, the employee class won't be able to identify method to use. Hence you have to include the required 3 input parameters within the round brackets.

http://msdn.microsoft.com/en-us/library/w86s7x04.aspx

http://msdn.microsoft.com/en-us/library/aa288470%28v=vs.71%29.aspx

http://www.tutorialspoint.com/csharp/csharp_properties.htm