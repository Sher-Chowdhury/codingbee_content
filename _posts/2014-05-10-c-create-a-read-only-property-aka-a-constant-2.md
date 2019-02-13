---
ID: 714
post_title: >
  c-create-a-read-only-property-aka-a-constant
author: admin
post_excerpt: ""
layout: post
permalink: http://localhost:8000/?p=714
published: true
post_date: 2014-05-10 00:00:00
---
So far we have looked at 5 ways to store data in a class's object (aka an instance) in the form of "properties". Of which one of the recommended ways of setting values to properties, is by using the constructor method, e.g.:

[csharp] using System; using System.Collections.Generic; using System.Linq; using System.Text; using System.Threading.Tasks;

class Employee { public string Firstname { get; set; } public int Age { get; set; } public double Salary { get; set; }

    public Employee(string firstname, int age, double salary)
    {
    
        Firstname = firstname;
        Age = age;
        Salary = salary;
    }

}

class Program { static void Main() { Employee Dave = new Employee("David", 35, 35235.23); Console.WriteLine("{0} is {1} years old and earns £{2} per year.", Dave.Firstname, Dave.Age.ToString(), Dave.Salary.ToString()); } } [/csharp]

Now lets say all the employees of the company earn the same salary of £30000, no matter if they are the cleaner or the CEO. In that case how can we reflect this in the above code? We can do this by expanding salary member and then change "set" to only accept a fix value, i.e. we can do:

[csharp] using System; using System.Collections.Generic; using System.Linq; using System.Text; using System.Threading.Tasks;

class Employee { public string Firstname { get; set; } public int Age { get; set; }

    private double salary;
    public double Salary
    {
        get
        {
            return salary;
        }
        set
        {
            this.salary = 30000;
        }
    }
    
    public Employee(string firstname, int age)
    {
    
        Firstname = firstname;
        Age = age;
        Salary = salary;     // You need to run this just to trigger the Salary property's get feature.  
    }

}

class Program { static void Main() { Employee Dave = new Employee("David", 35); Console.WriteLine("{0} is {1} years old and earns £{2} per year.", Dave.Firstname, Dave.Age.ToString(), Dave.Salary.ToString()); } } [/csharp]

In order to get this to work, we had to remove the constructor's "salary" input parameter, since it is now read only.