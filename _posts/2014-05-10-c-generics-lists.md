---
ID: 40
post_title: 'c# &#8211; Generics: Lists'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/c/c-generics-lists
published: true
post_date: 2014-05-10 00:00:00
---
Arrays have a big limitation is that you cant extend the array to hold more than the number items it was originally defined to hold.

As a result a lot of people uses "lists" as an alternative.

You can find the lists class in the following namespace:

System.Collections.Generic

Here is the link for the "generic" namespace

http://msdn.microsoft.com/en-us/library/system.collections.generic(v=vs.110).aspx

The word "generic" comes from the fact that lists (along with queues, stacks, ) can hold any kind of builtin object variables, e.g. string, int, double,...and etc.

The list's class is:

http://msdn.microsoft.com/en-us/library/6sh2ey19%28v=vs.110%29.aspx

(The "" indicates type)

Lists, as with all the generics comes with it's own style of syntax.....in the form of angle brackets, "&lt;=>". Inside these brackets you specify the data type that the list will hold.

The way lists works is that you first create an empty list, and then you add items to it. In the following example we create a list (ListOfRandomNumbers) that is designed to hold a series of random integers e.g.:

[csharp]
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace List
{
    class Program
    {
        static void Main(string[] args)
        {

			// Here you can think of &quot;List&quot;
			List ListOfRandomNumbers = new List();

			// first we create an instance of the random class
			Random RandomNumberGenerator = new Random();

			// Here we add random numbers to the list
			for (int i=0; i			{
				ListOfRandomNumbers.Add(RandomNumberGenerator.Next());
			}

			// Here we sort the numbers, using the list class's &quot;Sort&quot; method:
			ListOfRandomNumbers.Sort();

			foreach (int number in ListOfRandomNumbers)
			{
				Console.WriteLine(number);
			}
		}
    }

}
[/csharp]

lists can often be used as a way to get a list of items as an output parameter for a method. 


Also list's can also hold custom object, here's an example:

[csharp]
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace List
{
    class Program
    {
        static void Main(string[] args)
        {		
			// Here you can think of &quot;List&lt;int=>&quot; 
			List&lt;CountryStat=> ListOfCountryData = new List&lt;CountryStat=>();
			
			CountryStat EnglandObject = new CountryStat(&quot;London&quot;, 60000000, &quot;English&quot;);
			CountryStat FranceObject = new CountryStat(&quot;Paris&quot;, 90000000, &quot;French&quot;);
			CountryStat RussiaObject = new CountryStat(&quot;Moscow&quot;, 180000000, &quot;Russian&quot;);
			
			ListOfCountryData.Add(EnglandObject);
			ListOfCountryData.Add(FranceObject);
			ListOfCountryData.Add(RussiaObject);
			
			foreach (CountryStat CountryData in ListOfCountryData)
			{
				Console.WriteLine(CountryData.Capital);
			}
		}
    }

	public class CountryStat
    {

        public string Capital { get; set; }
        public int Population { get; set; }
        public string Language { get; set; }

        public CountryStat(string Capital, int Population, string Language)
        {
            this.Capital = Capital;
            this.Population = Population;
            this.Language = Language;
        }
	}
}
[/csharp]