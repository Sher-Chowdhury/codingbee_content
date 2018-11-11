---
ID: 16
post_title: 'c# &#8211; Instantiate an object and set it&#8217;s Properties simultaneously (using the Initialization approach)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/c/c-instantiate-an-object-and-set-its-properties-simultaneously-using-the-initialization-approach
published: true
post_date: 2014-05-10 00:00:00
---
&nbsp;
<h2>1st Approach: Using Initialization</h2>
Here we use properties (as demonstrated in the previous), but this time we set all the properties in one go, by writing them as part of the object's actual declaration, using curly-bracket syntax:

[csharp]
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

class Employee
{
	public string Firstname {get; set;}
	public int Age {get; set;}
	public double Salary {get; set;}
}

class Program
{
    static void Main()
    {
        Employee Dave = new Employee()
		{
			Firstname = &amp;quot;David&amp;quot;,
			Age = 35,
			Salary = 35235.23
		};
		// Using the above syntax is called initialisation

        Console.WriteLine(&amp;quot;{0} is {1} years old and earns  £{2} per year.&amp;quot;, Dave.Firstname, Dave.Age.ToString(), Dave.Salary.ToString());
    }
}
[/csharp]