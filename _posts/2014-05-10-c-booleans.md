---
ID: 26
post_title: 'c# &#8211; Booleans'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/c/c-booleans
published: true
post_date: 2014-05-10 00:00:00
---
booleans are variables that hold true or false.

&nbsp;

[csharp]

using System;

class BooleanDemo
{
	static void Main()
	{
		bool myFirstBoolean = 3 + 2 == 5;
		Console.WriteLine(myFirstBoolean.ToString());
		
		bool mySecondBoolean = 3 + 2 => 5;
		Console.WriteLine(mySecondBoolean.ToString());
		
		// Here we use the 'and', &quot;&amp;&amp;&quot; operator
		bool BothAreTrue = myFirstBoolean &amp;&amp; mySecondBoolean ;
		Console.WriteLine(&quot;Are both true: &quot; + BothAreTrue.ToString());

		// Here we use the 'or', &quot;||&quot; operator		
		bool IsOneTrue = myFirstBoolean || mySecondBoolean ;
		Console.WriteLine(&quot;Are both true: &quot; + IsOneTrue.ToString());

		// here we create a boolean that stores the opposite result
		// of another boolean using the &quot;not&quot; (!) operator:
		bool GetReverseInfo = !myFirstBoolean;
		Console.WriteLine(&quot;The opposite result of myFirstBoolean, is: &quot; + GetReverseInfo.ToString());
		
	}
}        
[/csharp]