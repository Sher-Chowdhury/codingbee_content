---
ID: 19
post_title: 'c# &#8211; Method Overloading: Have two or methods of the same name'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/c/c-method-overloading-have-two-or-methods-of-the-same-name
published: true
post_date: 2014-05-10 00:00:00
---
Method Overloading is basically a technique that lets you have different versions of the same method, where each version of the method does a different thing.

It essentially makes methods more versatile.

Each version of the method accepts different/types of input parameters.

The way you control which of version  method you want to trigger, is by specifying the correct number/type of input parameters for that corresponding method.

In the following example, we have 2 methods, which in this case achieves the same goal by different means. The here is to instantiate an object:

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

	// we are going to use a technique called method overloading
	// as a way to have 2 methods of the same name, 
	// and leave it to c# to work out which method we want based 
	// on the input parameter provided when the method is called. 

    public Employee(string firstname, int age, double salary)
    {
        
        Firstname = firstname;
        Age = age;
        Salary = salary;
    }

    public Employee()
    { }

}
 
class Program
{
    static void Main()
    {
        
		// Here we initialised the object using the 
		//default constructor
		// to create the empty memory container.
		Employee Dave = new Employee()
        {
            Firstname = &quot;David&quot;,
            Age = 35,
            Salary = 35235.23
        };
        Console.WriteLine(&quot;{0} is {1} years old and earns  £{2} per year.&quot;, Dave.Firstname, Dave.Age.ToString(), Dave.Salary.ToString());

		// Here we used the constructor to create
        Employee Jenny = new Employee(&quot;Jennifer&quot;, 22, 25023.50);
        
		Console.WriteLine(&quot;{0} is {1} years old and earns  £{2} per year.&quot;, Jenny.Firstname, Jenny.Age.ToString(), Jenny.Salary.ToString());
    }
}
[/csharp]