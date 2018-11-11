---
ID: 15
post_title: 'c# &#8211; Storing data inside objects (by using &#8220;Properties&#8221;)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/c/c-storing-data-inside-objects-by-using-properties
published: true
post_date: 2014-05-10 00:00:00
---
Previously we looked at how to store data in an object using fields. Fields are easy to use, but only if you set them to public. Having public fields is bad practice.

To overcome this we can set the fields to  private and then write (public) methods within the class to access/modify the fields. This approach keeps the fields private, but unfortunately it is a much more messier solution....that requires writing a lot more code, and is more harder to follow.

That's where "properties" comes in to save the day. Properties, gives you the best of both worlds, i.e.:
<ul>
	<li>It keeps the object's data private</li>
	<li>It keeps the syntax simple and has minimal overhead in terms of number of lines.</li>
</ul>
Properties are type of class member that is specifically used for storing an object's data. Properties have their own syntax as well, as you will see now:

[csharp]
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

class Employee
{
    // First we declare the variable that we want the property member
    // to set/get. By convention, it must be in lower case,
    // e.g. not something like &quot;FirstName&quot;
    private string firstname;

    // Here we are creating a property called &quot;Firstname&quot;
    // By convention, the property's name is capitalised....to differentiate it
    // from the variable.
    // Notice that the first line doesn't end with round brackets, e.g.
    // &quot;private string firstname()&quot;, this helps to distinguish
    // a property from a method.
    public string Firstname
    {
        get
        {
            return firstname;
        }
        set
        {
            firstname = value;
            // Note: &quot;value&quot; is a special built-in keyword of properties.
            // It acts as hidden default input-parameter.
        }
    }

    private int age;
    public int Age
    {
        get
        {
            return age;
        }
        set
        {
            age = value;
        }
    }

    private double salary;
    public double Salary
    {
        get
        {
            return salary;
        }
        set
        {
            salary = value;
        }
    }
}

class Program
{
    static void Main(string[] args)
    {
        Employee Dave = new Employee();

        // Notice here that we are calling the
        // property, as indicated by the capitalised first letter,
        // The value we provide, automatically get's assigned to the properties
        // builtin &quot;value&quot; input parameter.
        Dave.Age = 50;
        Dave.Firstname = &quot;David&quot;;
        Dave.Salary = 35461.32;
        // Notice we also didn't have to do anything like:
        // Dave.Age()....
        // Dave.Age.get()
        // These are unnecessary, and would be wrong anyway.

        // Note, the property's &quot;set&quot; option was triggered in the above three
        // lines because we used the &quot;=&quot; sign to assign values.
        // But the &quot;get&quot; option gets triggered below, because we are calling
        // it.

        Console.WriteLine(&quot;{0} is {1} years old and earns  £{2} per year.&quot;, Dave.Firstname, Dave.Age.ToString(), Dave.Salary.ToString());
    }
}
[/csharp]

The cool thing about using properties, is :
- when you are assigning values to properties, it appears like you are just assigning variable directly, i.e. you are assigning values using the equal sign rather then passing through via brackets (which you do in the methods option).
- the employee class's internal variables (which are in lower case) remain private, and the input values it receives get handled in a way that's similar to using methods.
- you can control get/set abilities of the properties, by omitting or modifying them.

Note, you rewrite above to make it shorter by removing a lot of whitespace, i.e.:

[csharp]
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

class Employee
{
    private string firstname;
    public string Firstname
    {
        get{return firstname;}
        set{firstname = value;}
    }

    private int age;
    public int Age
    {
        get{return age;}
		set{age = value;}
    }

    private double salary;
    public double Salary
    {
        get{return salary;}
        set{salary = value;}
    }
}

class Program
{
    static void Main(string[] args)
    {
        Employee Dave = new Employee();

        Dave.Age = 50;
        Dave.Firstname = &quot;David&quot;;
        Dave.Salary = 35461.32;

        Console.WriteLine(&quot;{0} is {1} years old and earns  £{2} per year.&quot;, Dave.Firstname, Dave.Age.ToString(), Dave.Salary.ToString());
    }
}
[/csharp]

However, since properties including get/set are so commonly used, Microsoft has introduced an even shorter version of the above syntax:

[csharp]
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

class Employee
{
    //private string firstname;
    //public string Firstname
    //{
    //    get{return firstname;}
    //    set{firstname = value;}
    //}
    // The compiler will expand the following line
    // into the above code block:
	public string Firstname {get; set;}

	// Likewise we have:
	public int Age {get; set;}
	public double Salary {get; set;}

}

class Program
{
    static void Main(string[] args)
    {
        Employee Dave = new Employee();

        Dave.Age = 50;
        Dave.Firstname = &quot;David&quot;;
        Dave.Salary = 35461.32;

        Console.WriteLine(&quot;{0} is {1} years old and earns  £{2} per year.&quot;, Dave.Firstname, Dave.Age.ToString(), Dave.Salary.ToString());
    }
}
[/csharp]

Note: the above "{get; set;}" syntax is indicator that the given item is a property.

&nbsp;

Note: the terminology "getter" and "setter" are used to refer to these special get and set methods.