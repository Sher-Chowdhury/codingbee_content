---
ID: 31
post_title: 'c# &#8211; The &#8220;this&#8221; keyword'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/c/c-the-this-keyword
published: true
post_date: 2014-05-10 00:00:00
---
Earlier we cam across the following:

[csharp]
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

class Employee
{
    public string Firstname { get; set; }
    //Note, the above line is the shorthand way of writing the following:
	//
	//   private string firstname;   // here we are defining a property called &quot;firstname&quot;
    //   public string Firstname
    //   {
    //        get{return firstname;}
    //        set{firstname = value;}
    //   }
	//
	// The same could be done for the following two lines

	public int Age { get; set; }
    public double Salary { get; set; }

    public Employee(string Firstname, int age, double Salary)
    {

        this.Firstname = Firstname;
        this.Age = Age;
        this.Salary = Salary;
    }

}

class Program
{
    static void Main()
    {
        Employee Jenny = new Employee(&quot;Jennifer&quot;, 22, 25023.50);
        Console.WriteLine(&quot;{0} is {1} years old and earns  £{2} per year.&quot;, Jenny.Firstname, Jenny.Age.ToString(), Jenny.Salary.ToString());
    }
}
[/csharp]

If you look at the above code, you will notice that the employee class's constructor has input parameters that are called Firstname, Salary, and Age. But these names are also used as the names of the Employee class's properties.

As a result it can get a bit confusing as to what we are referring to, and consequently the above code will result in an error message:

<img alt="" src="data:image/png;base64,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" />

One way to  fix this is by using changing the names for the properties/input-parameters.  However you would often want the names to match as a way to make it easier to see the link between an input parameter, and the property it sets/gets. That's why a better way is by using the "this" keyword as a label the members that are internal to the class (e.g. private members), i.e. we can rewrite the above code to:

[csharp]using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

class Employee
{
    public string Firstname { get; set; }	
	public int Age { get; set; }
    public double Salary { get; set; }
	
 
    public Employee(string Firstname, int Age, double Salary)
    {
        
        this.Firstname = Firstname;     // the &quot;this&quot; keyword that the 
        this.Age = Age;                 // left hand side of the equation 
        this.Salary = Salary;           // refers to the property.
    }

}

class Program
{
    static void Main()
    {
        Employee Jenny = new Employee(&quot;Jennifer&quot;, 22, 25023.50);
        Console.WriteLine(&quot;{0} is {1} years old and earns  £{2} per year.&quot;, Jenny.Firstname, Jenny.Age.ToString(), Jenny.Salary.ToString());
    }
}[/csharp]