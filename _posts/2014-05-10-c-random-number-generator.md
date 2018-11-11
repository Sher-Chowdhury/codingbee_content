---
ID: 37
post_title: 'c# &#8211; Random Number Generator'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/c/c-random-number-generator
published: true
post_date: 2014-05-10 00:00:00
---
Here's a quick a simple class for creating a a series of random numbers, by creating an instance of the "Random" class, and applying this class's next() method:
 
http://msdn.microsoft.com/en-us/library/system.random%28v=vs.110%29.aspx

Here is the code:

[csharp]
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace RandomNumberGenerator
{
    class Program
    {
        static void Main(string[] args)
        {
			Random RandomNumber = new Random();
			
			Console.WriteLine(&quot;Here is a list of random numbers:&quot;);
			Console.WriteLine(RandomNumber.Next());
			Console.WriteLine(RandomNumber.Next());
			Console.WriteLine(RandomNumber.Next());
			Console.WriteLine(RandomNumber.Next());
			Console.WriteLine(RandomNumber.Next());
			
		}
    }
	
}
[/csharp]