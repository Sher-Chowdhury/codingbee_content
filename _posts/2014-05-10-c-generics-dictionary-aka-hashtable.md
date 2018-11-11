---
ID: 43
post_title: 'c# &#8211; Generics: Dictionary (aka hashtable)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/c/c-generics-dictionary-aka-hashtable
published: true
post_date: 2014-05-10 00:00:00
---
In c#, dictionaries are the same thing as hashtables.

Dictonaries are a bit like arrays where you can customise the default index numbers to something more meaningful, here's an example:

[csharp]
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace Dictionary
{
    class Program
    {
        static void Main(string[] args)
        {
			// here we create an empty dictionary
			// called &quot;CountriesAndCapitals&quot;
			// where we define that
			// the index, is a string, and the main value is
			// also a string:
			Dictionary&lt;string, string=> CountriesAndCapitals = new Dictionary&lt;string, string=>();

			// Here we add items to the dictionary
			CountriesAndCapitals.Add(&quot;England&quot;,&quot;London&quot;);
			CountriesAndCapitals.Add(&quot;France&quot;,&quot;Paris&quot;);
			CountriesAndCapitals.Add(&quot;Italy&quot;,&quot;Rome&quot;);

			Console.WriteLine(CountriesAndCapitals[&quot;Italy&quot;]);
		}
    }
}
[/csharp]

Dictionaries are really powerful, and one of things it often gets used for is to create a dictionary where the key is string, but the value itself is actually a custom object, and to instantiate this type of object we do:

Dictionary<string, {Custom Object's Name}> {Dictionary's Name} = new Dictionary<string, {Custom Class's Name}>();


Now something that is common practice is that we create a custom class, which we'll use to create the custom objects needed to populate the dictionary. A (static) method (that gets triggered by the main program), which in turn instantiates the dictionary object, the method then instantiates a number of objects from the custom class and adds them to the dictionary along with a key. Finally the method returns the dictionary back to it's caller, which would capture it in a dictionary-variable.

Here is an example of this in action:

[csharp]
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace Dictionary
{
    class Program
    {
        static void Main(string[] args)
        {

            // Here we call the CountryStat's class's (static) GetCountryListInfo method,
            // in order for it to generate the dictionary, populate it, and 
            // then return the dictionary:
            var RetrievedCountryInfo = CountryStat.GetCountryListInfo();

            Console.WriteLine
			(
				&quot;England has a population of {0} and it's official language is {1}. It's capital city is called {2}.&quot;,
				RetrievedCountryInfo[&quot;England&quot;].Population,
				RetrievedCountryInfo[&quot;England&quot;].
				Language,RetrievedCountryInfo[&quot;England&quot;].Capital
			);
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

        // Here we instantiate the dictionary, and add
        // entries to the dictionary
        public static Dictionary&lt;string, CountryStat=> GetCountryListInfo()
		{
			// Here we create the empty dictionary that 
			// we will populate:
			Dictionary&lt;string, CountryStat=> DictionaryOfCountries = new Dictionary&lt;string, CountryStat=>();
			
			// Here we create the first object to be added to the dictionary:
			CountryStat EnglandObject = new CountryStat(&quot;London&quot;, 60000000, &quot;English&quot;);
			// Now we add this object to the dictionary:
			DictionaryOfCountries.Add(&quot;England&quot;,EnglandObject);

			// Here we create the next object to be added to the dictionary:
			CountryStat FranceObject = new CountryStat(&quot;Paris&quot;, 90000000, &quot;French&quot;);
			// Now we add this object to the dictionary:
			DictionaryOfCountries.Add(&quot;France&quot;,FranceObject);
			
			
			// Here we create the next object to be added to the dictionary:
			CountryStat RussiaObject = new CountryStat(&quot;Moscow&quot;, 180000000, &quot;Russian&quot;);
			// Now we add this object to the dictionary:
			DictionaryOfCountries.Add(&quot;Russia&quot;,RussiaObject);
			
			// Now we return the populated dictionary back to the caller:
			return DictionaryOfCountries;		
		}
    }
}
[/csharp]