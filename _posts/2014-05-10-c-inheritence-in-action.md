---
ID: 34
post_title: 'c# &#8211; Inheritence in Action'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/c/c-inheritence-in-action
published: true
post_date: 2014-05-10 00:00:00
---
Lets say we have a supermarket and it sells a different types of fruits, then you could create a class for each type of fruit:

The above will output:

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
			// Here we create an object using the orange class
            Orange AnOrange = new Orange(1.25, 45, &quot;Spain&quot;, &quot;25-01-2015&quot;, &quot;sweet&quot;, true, &quot;clementine&quot;);
			string ContainsSeedsSentence;
            if (AnOrange.ContainsSeeds)
            {
                ContainsSeedsSentence = &quot;This fruit contains seeds.&quot;;
            }
            else
            {
                ContainsSeedsSentence = &quot;This fruit is seedless.&quot;;
            }

            Console.WriteLine(&quot;\n\nThis {0} is a {1} and is grown in {2} and costs  £{3}. It's a {4} fruit and contains {5} calories. It's use-by date is {6}. {7}&quot;,
            AnOrange.FruitName, AnOrange.OrangeType, AnOrange.CountryOfOrigin, AnOrange.Price, AnOrange.SweetnessLevel, AnOrange.Calories, AnOrange.UseByDate, ContainsSeedsSentence);

			// Here we create an object using the banana class
            Banana ABanana = new Banana(0.43, 135, &quot;Kenya&quot;, &quot;25-05-2014&quot;, &quot; very sweet&quot;, false, &quot;Cavendish&quot;);
        	if (ABanana.ContainsSeeds)
            {
                ContainsSeedsSentence = &quot;This fruit contains seeds.&quot;;
            }
            else
            {
                ContainsSeedsSentence = &quot;This fruit is seedless.&quot;;
            }
            Console.WriteLine(&quot;\n\nThis {0} is a {1} and is grown in {2} and costs  £{3}. It's a {4} fruit and contains {5} calories. It's use-by date is {6}. {7}&quot;,
            ABanana.FruitName, ABanana.BananaType, ABanana.CountryOfOrigin, ABanana.Price, ABanana.SweetnessLevel, ABanana.Calories, ABanana.UseByDate, ContainsSeedsSentence);

        }
    }

    class Orange
    {

		private string fruitname;
        public string FruitName
		{
			get{return fruitname;}
			set{fruitname = &quot;Orange&quot;;}
		}
		// notice that we have expanded the Fruitname property above, because we want it to be a constant.
		// All other properties are defined in the standard way below:
        public double Price { get; set; }
        public int Calories { get; set; }
        public string CountryOfOrigin { get; set; }
        public string UseByDate { get; set; }
        public string SweetnessLevel { get; set; }
        public bool ContainsSeeds { get; set; }
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

    class Banana
    {
		private string fruitname;
        public string FruitName
		{
			get{return fruitname;}
			set{fruitname = &quot;Banana&quot;;}
		}
		// notice that we have expanded the Fruitname property above, because we want it to be a constant.
		// All other properties are defined in the standard way below:
        public double Price { get; set; }
        public int Calories { get; set; }
        public string CountryOfOrigin { get; set; }
        public string UseByDate { get; set; }
        public string SweetnessLevel { get; set; }
        public bool ContainsSeeds { get; set; }
        public string BananaType { get; set; }

        public Banana(double Price, int Calories, string CountryOfOrigin, string UseByDate, string SweetnessLevel, bool ContainsSeeds, string BananaType)
        {
			FruitName = FruitName;   // This is inserted to trigger the FruitName property's set element.
            this.Price = Price;
            this.Calories = Calories;
            this.CountryOfOrigin = CountryOfOrigin;
            this.UseByDate = UseByDate;
            this.SweetnessLevel = SweetnessLevel;
            this.ContainsSeeds = ContainsSeeds;
            this.BananaType = BananaType;
        }
    }
}
[/csharp]

The resulting output would be:

<img alt="" src="data:image/png;base64,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" />

Now there is a good chance that this supermarket sells a lot of different types of fruits, e.g apples, melons, mangoes, etc.

So what we could do is write a class for each fruit, however if we did this, we would discover that there are a lot of repeating members within each fruit class.

For example in the above 2 classes, orange and banana you will have noticed that these 2 classes had the following member in common:

this.Price = Price;
this.Calories = Calories;
this.CountryOfOrigin = CountryOfOrigin;
this.UseByDate = UseByDate;
this.SweetnessLevel = SweetnessLevel;
this.ContainsSeeds = ContainsSeeds;

The only two properties that were different in each class were:
<p style="padding-left: 30px;">fruitname         (they had different "set" values)
this.BananaType      (unique to banana class)
this.OrangeType      (unique to orange class)</p>
In this scenario, this presents an ideal scenario to take advantage of the concept of inheritence, to rewrite the code in fewer lines and make it more organised/structured at the same time.

Inheritance is to do with recognising which classes are actually are special versions (aka sub-classes) of more general classes. We do this by looking for classes that are similar (both in what they contain and what they represent) and then group them together.

We can then create a high-level class (aka base class) and move the common members out of the sub-classes and place them into the base-class.

For example orange, and banana both represent the same kind of logical object, which is a "fruit". Hence we can organise our code by creating a new "fruit" class:

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
            // Here we create an object using the orange class
            Orange AnOrange = new Orange(1.25, 45, &quot;Spain&quot;, &quot;25-01-2015&quot;, &quot;sweet&quot;, true, &quot;clementine&quot;);
            string ContainsSeedsSentence;
            if (AnOrange.ContainsSeeds)
            {
                ContainsSeedsSentence = &quot;This fruit contains seeds.&quot;;
            }
            else
            {
                ContainsSeedsSentence = &quot;This fruit is seedless.&quot;;
            }

            Console.WriteLine(&quot;\n\nThis {0} is a {1} and is grown in {2} and costs  £{3}. It's a {4} fruit and contains {5} calories. It's use-by date is {6}. {7}&quot;,
            AnOrange.FruitName, AnOrange.OrangeType, AnOrange.CountryOfOrigin, AnOrange.Price, AnOrange.SweetnessLevel, AnOrange.Calories, AnOrange.UseByDate, ContainsSeedsSentence);

            // Here we create an object using the banana class
            Banana ABanana = new Banana(0.43, 135, &quot;Kenya&quot;, &quot;25-05-2014&quot;, &quot; very sweet&quot;, false, &quot;Cavendish&quot;);
            if (ABanana.ContainsSeeds)
            {
                ContainsSeedsSentence = &quot;This fruit contains seeds.&quot;;
            }
            else
            {
                ContainsSeedsSentence = &quot;This fruit is seedless.&quot;;
            }
            Console.WriteLine(&quot;\n\nThis {0} is a {1} and is grown in {2} and costs  £{3}. It's a {4} fruit and contains {5} calories. It's use-by date is {6}. {7}&quot;,
            ABanana.FruitName, ABanana.BananaType, ABanana.CountryOfOrigin, ABanana.Price, ABanana.SweetnessLevel, ABanana.Calories, ABanana.UseByDate, ContainsSeedsSentence);
            Console.ReadLine();
        }
    }

	// Here we created a new class that is the baseclass (aka the parent class)
	// of the orange and banana class.
    class Fruit {

        public double Price { get; set; }
        public int Calories { get; set; }
        public string CountryOfOrigin { get; set; }
        public string UseByDate { get; set; }
        public string SweetnessLevel { get; set; }
        public bool ContainsSeeds { get; set; }

    }

	// Here we used the &quot;:&quot; notation to indicate that Orange is a child-class of the Fruit.
	// It means that all the members (which at the moment are properties) that are defined in the
	// base class, are also implicitly defined in the child class.
    class Orange : Fruit
    {

        private string fruitname;
        public string FruitName
        {
            get { return fruitname; }
            set { fruitname = &quot;Orange&quot;; }
        }
        // notice that we have expanded the Fruitname property above, because we want it to be a constant.
        // All other properties are defined in the standard way below:

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

	// Here we used the &quot;:&quot; notation to indicate that Orange is a child-class of the Fruit.
	// It means that all the members (which at the moment are properties) that are defined in the
	// base class, are also implicitly defined in the child class.
    class Banana : Fruit
    {
        private string fruitname;
        public string FruitName
        {
            get { return fruitname; }
            set { fruitname = &quot;Banana&quot;; }
        }
        public string BananaType { get; set; }

        public Banana(double Price, int Calories, string CountryOfOrigin, string UseByDate, string SweetnessLevel, bool ContainsSeeds, string BananaType)
        {
            FruitName = FruitName;   // This is inserted to trigger the FruitName property's set element.
            this.Price = Price;
            this.Calories = Calories;
            this.CountryOfOrigin = CountryOfOrigin;
            this.UseByDate = UseByDate;
            this.SweetnessLevel = SweetnessLevel;
            this.ContainsSeeds = ContainsSeeds;
            this.BananaType = BananaType;
        }
    }
}
[/csharp]

This again gives the same output. Note also that the class "fruit" is something we might never to use directly, it is just being used here as way of oraganising/structuring/streamlining our code as well as categorising our classes.

Also, as you can see, the base class essentially "inherits" the members of it's parent class. That where this term comes from.

Now, a supermarket might sell other things on top of fruits. E.g. meat products, such as beef, chicken, fish,....etc.

In this scenario we can take a similar approach that we did for fruits, and create a parent class called "meat", and then subclasses for beef, chicken, and etc.

In this scenario, we would end up with 2 parent classes, "fruit" and "meat". And both of these parent-classes are very likely to have a number of properties in common, e.g. price.

So what we can do is make both the fruit and meat classes themselves sub classes of an even higher level class, e.g a class called "food", which in turn can be a subclass itself for an even higher level class called "Item". The item class could cover non-food items e.g. newspapers, washing-up liquid,....etc.

If we just stick to the fruit example for now, we can organize/structure the above code even more by creating the "food" and "item" classes:

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
            // Here we create an object using the orange class
            Orange AnOrange = new Orange(1.25, 45, &quot;Spain&quot;, &quot;25-01-2015&quot;, &quot;sweet&quot;, true, &quot;clementine&quot;);
            string ContainsSeedsSentence;
            if (AnOrange.ContainsSeeds)
            {
                ContainsSeedsSentence = &quot;This fruit contains seeds.&quot;;
            }
            else
            {
                ContainsSeedsSentence = &quot;This fruit is seedless.&quot;;
            }

            Console.WriteLine(&quot;\n\nThis {0} is a {1} and is grown in {2} and costs  £{3}. It's a {4} fruit and contains {5} calories. It's use-by date is {6}. {7}&quot;,
            AnOrange.FruitName, AnOrange.OrangeType, AnOrange.CountryOfOrigin, AnOrange.Price, AnOrange.SweetnessLevel, AnOrange.Calories, AnOrange.UseByDate, ContainsSeedsSentence);

            // Here we create an object using the banana class
            Banana ABanana = new Banana(0.43, 135, &quot;Kenya&quot;, &quot;25-05-2014&quot;, &quot; very sweet&quot;, false, &quot;Cavendish&quot;);
            if (ABanana.ContainsSeeds)
            {
                ContainsSeedsSentence = &quot;This fruit contains seeds.&quot;;
            }
            else
            {
                ContainsSeedsSentence = &quot;This fruit is seedless.&quot;;
            }
            Console.WriteLine(&quot;\n\nThis {0} is a {1} and is grown in {2} and costs  £{3}. It's a {4} fruit and contains {5} calories. It's use-by date is {6}. {7}&quot;,
            ABanana.FruitName, ABanana.BananaType, ABanana.CountryOfOrigin, ABanana.Price, ABanana.SweetnessLevel, ABanana.Calories, ABanana.UseByDate, ContainsSeedsSentence);
            Console.ReadLine();
        }
    }

    class Item
    {
        public double Price { get; set; }
        public string CountryOfOrigin { get; set; }
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
        // notice that we have expanded the Fruitname property above, because we want it to be a constant.
        // All other properties are defined in the standard way below:

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

    class Banana : Fruit
    {
        private string fruitname;
        public string FruitName
        {
            get { return fruitname; }
            set { fruitname = &quot;Banana&quot;; }
        }
        public string BananaType { get; set; }

        public Banana(double Price, int Calories, string CountryOfOrigin, string UseByDate, string SweetnessLevel, bool ContainsSeeds, string BananaType)
        {
            FruitName = FruitName;   // This is inserted to trigger the FruitName property's set element.
            this.Price = Price;
            this.Calories = Calories;
            this.CountryOfOrigin = CountryOfOrigin;
            this.UseByDate = UseByDate;
            this.SweetnessLevel = SweetnessLevel;
            this.ContainsSeeds = ContainsSeeds;
            this.BananaType = BananaType;
        }
    }
}
[/csharp]