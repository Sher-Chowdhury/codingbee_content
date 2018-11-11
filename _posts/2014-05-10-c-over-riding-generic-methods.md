---
ID: 36
post_title: 'c# &#8211; Over-riding generic methods'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/c/c-over-riding-generic-methods
published: true
post_date: 2014-05-10 00:00:00
---
Child  classes can inherit methods from it's parent class, in the same way that it inherits properties. Here is an example of the Orange class inheriting the "SomeInfo" method, which have been originally defined in the Item class:

&nbsp;
[csharp]
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace InheritenceInAction
{
    class Program
    {
        static void Main(string[] args)
        {
            Orange AnOrange = new Orange(1.25, 45, &quot;Spain&quot;, &quot;25-01-2015&quot;, &quot;sweet&quot;, true, &quot;clementine&quot;);

            // The &quot;SomeInfo&quot; method hasn't been explicitly defined in the Orange class, 
			// Howeever the following method call works thanks to inheritence.
            AnOrange.SomeInfo();
        }
    }

    class Item
    {
        public double Price { get; set; }
        public string CountryOfOrigin { get; set; }

        public virtual void SomeInfo()
        {
            Console.WriteLine(&quot;This item costs: {0}&quot;, Price);
        }
    }

    class Food : Item
    {
        public int Calories { get; set; }
        public string UseByDate { get; set; }
    }

    class Fruit : Food
    {

        public string SweetnessLevel { get; set; }
        public bool ContainsSeeds { get; set; }

    }

    class Orange : Fruit
    {
		// Notice, that this class doesn't explicitly contain a method called &quot;SomeInfo&quot;, however it does 
		// exist implicitly due to inheritence. 

        private string fruitname;
        public string FruitName
        {
            get { return fruitname; }
            set { fruitname = &quot;Orange&quot;; }
        }

        public string OrangeType { get; set; }

        public Orange(double Price, int Calories, string CountryOfOrigin, string UseByDate, string SweetnessLevel, bool ContainsSeeds, string OrangeType)
        {
            FruitName = FruitName;		// This is inserted to trigger the FruitName property's set element. 
            this.Price = Price;
            this.Calories = Calories;
            this.CountryOfOrigin = CountryOfOrigin;
            this.UseByDate = UseByDate;
            this.SweetnessLevel = SweetnessLevel;
            this.ContainsSeeds = ContainsSeeds;
            this.OrangeType = OrangeType;
        }
    }
}
[/csharp]

&nbsp;

However you can make some changes to the above code to allow the child class t either over-ride the inherited  method, or add extra tasks before/after the method. This is done by adding the "virtual" and "override" labels:

[csharp]
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace InheritedMethodOverrided
{
    class Program
    {
        static void Main(string[] args)
        {
            Orange AnOrange = new Orange(1.25, 45, &quot;Spain&quot;, &quot;25-01-2015&quot;, &quot;sweet&quot;, true, &quot;clementine&quot;);

            AnOrange.SomeInfo();
        }
    }

    class Item
    {
        public double Price { get; set; }
        public string CountryOfOrigin { get; set; }

		// The &quot;virtual&quot; keyword here is telling all the child classes that they 
		// can override this method if they want to.
        public virtual void SomeInfo()
        {
            Console.WriteLine(&quot;This item costs: {0}&quot;, Price);
        }
    }

    class Food : Item
    {
        public int Calories { get; set; }
        public string UseByDate { get; set; }
    }

    class Fruit : Food
    {

        public string SweetnessLevel { get; set; }
        public bool ContainsSeeds { get; set; }

    }

    class Orange : Fruit
    {
 
        private string fruitname;
        public string FruitName
        {
            get { return fruitname; }
            set { fruitname = &quot;Orange&quot;; }
        }

        public string OrangeType { get; set; }

        public Orange(double Price, int Calories, string CountryOfOrigin, string UseByDate, string SweetnessLevel, bool ContainsSeeds, string OrangeType)
        {
            FruitName = FruitName;
            this.Price = Price;
            this.Calories = Calories;
            this.CountryOfOrigin = CountryOfOrigin;
            this.UseByDate = UseByDate;
            this.SweetnessLevel = SweetnessLevel;
            this.ContainsSeeds = ContainsSeeds;
            this.OrangeType = OrangeType;
        }


        // The &quot;override&quot; keyword here means that we are overriding the inherited  
        // method. 
        public override void SomeInfo()
        {
            Console.WriteLine(&quot;override successful&quot;);
        }


    }
}

[/csharp]
In the above example we did a complete override. But what if we want to run the inherited method as is, but just prefix/suffix (or both prefix and suffix) the method with some additional c# statement, in that case we can use the "base." syntax like this:

[csharp]
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace InheritedMethodOverrided
{
    class Program
    {
        static void Main(string[] args)
        {
            Orange AnOrange = new Orange(1.25, 45, &quot;Spain&quot;, &quot;25-01-2015&quot;, &quot;sweet&quot;, true, &quot;clementine&quot;);

            AnOrange.SomeInfo();
        }
    }

    class Item
    {
        public double Price { get; set; }
        public string CountryOfOrigin { get; set; }

		// The &quot;virtual&quot; keyword here is telling all the child classes that they 
		// can override this method if they want to.
        public virtual void SomeInfo()
        {
            Console.WriteLine(&quot;This item costs: {0}&quot;, Price);
        }
    }

    class Food : Item
    {
        public int Calories { get; set; }
        public string UseByDate { get; set; }
    }

    class Fruit : Food
    {

        public string SweetnessLevel { get; set; }
        public bool ContainsSeeds { get; set; }

    }

    class Orange : Fruit
    {
 
        private string fruitname;
        public string FruitName
        {
            get { return fruitname; }
            set { fruitname = &quot;Orange&quot;; }
        }

        public string OrangeType { get; set; }

        public Orange(double Price, int Calories, string CountryOfOrigin, string UseByDate, string SweetnessLevel, bool ContainsSeeds, string OrangeType)
        {
            FruitName = FruitName;
            this.Price = Price;
            this.Calories = Calories;
            this.CountryOfOrigin = CountryOfOrigin;
            this.UseByDate = UseByDate;
            this.SweetnessLevel = SweetnessLevel;
            this.ContainsSeeds = ContainsSeeds;
            this.OrangeType = OrangeType;
        }


        // The &quot;override&quot; keyword here means that we are overriding the inherited  
        // method. 
        public override void SomeInfo()
        {
            Console.WriteLine(&quot;prefix override successful&quot;);
            base.SomeInfo();  // the &quot;base&quot; keyword makes the original inherited method to run. 
            Console.WriteLine(&quot;Suffix override successful&quot;);

        }

    }
}
[/csharp]