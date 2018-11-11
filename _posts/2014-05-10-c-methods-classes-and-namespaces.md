---
ID: 13
post_title: 'c# &#8211; Methods, Classes, and Namespaces'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/c/c-methods-classes-and-namespaces
published: true
post_date: 2014-05-10 00:00:00
---
When programming it is essential to organize and structure your code, otherwise it will become unwieldy to work with.

c# offer a number of ways to structure and and organize our code using:
<ul>
	<li>methods</li>
	<li>classes</li>
	<li>namespaces</li>
</ul>
Methods are used for storing blocks of codes and each method is designed to perform a specific task (aka function).

Methods, in turn, are stored in "classes". These classes  class can store one or more methods.

Classes in turn are stored in "namespaces".  You can think of Namespaces as a bit like folders on a windows machine. That means that namespaces have tree structures where a namespace can house classes as well as other lower-level namespaces.  As a result we have the following hierarchy:

namespaces => classes => methods

One of the key strength of c# is that it has access to a immensely powerful pre-installed namespace, that is in the form of the .net framework. This framework has a library containing thousands of ready-made classes that you can make use of for building your own applications.

The top level namespace in the .net framework is called "System". All  classes and lower-level namespaces are housed under the System tree structure. The <a href="http://msdn.microsoft.com/en-us/library/system%28v=vs.110%29.aspx" target="_blank">.net Framework library</a> website fully documents all the classes and their method.

With this in mind, let's revisit our hello world example again:

[csharp]

using System;
class HelloWorld
{
    static void Main()
	{
		string message = &quot;Hello World&quot;;
		Console.WriteLine (message);
                Console.ReadLine();
	}
}

[/csharp]

<strong>Line 1</strong> - Here we are telling our program to load all the classes stored directly under the "System" namespace into memory. This won't load any classes that are stored in other namespaces under System.  As a result, one of the classes that will be loaded into memory, will be a class called "Console".

<strong>Line 7</strong> - Here we are using the "<a href="http://msdn.microsoft.com/en-us/library/system.console%28v=vs.110%29.aspx" target="_blank">Console</a>" class (which we loaded into memory in line 1). In particular we are using a "." notation to specify that we want to use the "<a href="http://msdn.microsoft.com/en-us/library/xf2k8ftb%28v=vs.110%29.aspx" target="_blank">WriteLine</a>" method within the console line, to write something to the terminal.

<strong>Line 8</strong> - Here we are using the Console class again, but this time we are using the <a href="http://msdn.microsoft.com/en-us/library/system.console.readline%28v=vs.110%29.aspx" target="_blank">ReadLine</a>. Strictly speaking ReadLine should be used for capturing anything that a user types into the terminal, but here we are using to keep the terminal open.

[vision_notification style="neutral" font_size="12px" closeable="false"] You can identify a method, by the fact that a method is always followed by a pair of round brackets. These round brackets can either contain some content or are empty, depending on the type of method you are running. [/vision_notification]

In Line 1, the program loaded all the classes that are in the "System" namespace. However another way to tell the program where to find a class, is by using a class's fully qualfied name, like this:

[csharp]
// the &quot;using System;&quot; is no longer needed.
class HelloWorld
{
    static void Main()
	{
		string message = &quot;Hello World&quot;;
		System.Console.WriteLine (message);
                System.Console.ReadLine();
	}
}

[/csharp]

Line 7 - here we used the full path of the class, along with which class's method to use. The general syntax that we used to drill down to the is as follows:
<pre>namespace1.namespace2...namespaceX.classname.methodname()</pre>
Lets take a look at another example, let's say we want a program that works out how many days there are for a given number of weeks. Then we can create something like this:

[csharp]
using System;
class sampleclass
{
	static void Main()
	{
                int NumberOfDays = WeekToDays (4)
		Console.WriteLine (NumberOfDays);      // 28
	}

	static int WeekToDays (int Week)
	{
		int days = Week * 7;
		return days;
	}
}
[/csharp]

In this example, we have a class that contains 2 methods, they are "Main" and "WeekToDays". Also notice that the "Main" method actually triggers the "WeekToDays" method as part of its own running.

This program will work out how many days there are in 4 weeks. In this case running this program will display "28".

Line weektodays - you will notice that the round brackets contains some content, this is the input parameter. Also just before the "WeekToDays" we stated "int", this represents the form of the output parameter. We will cover more about input and output parameters later.

Classes essentially lets you define/create your very own custom types, on top of the types that are come included with .net, e.g. int, string, bool, console, etc.