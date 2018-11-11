---
ID: 12
post_title: 'c# &#8211; Adding comments to your code'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/c/c-adding-comments-to-your-code
published: true
post_date: 2014-05-10 00:00:00
---
It is always good practice to insert comments into your code so that you (as well as others) can understand what your code is doing.

You can place a single line comment after a line of code, or on a line of it's own using the "//" notation.

If you want to insert a multi-line comment, then you can begin it using /* and end the comment with */.

Here we the 2 comments types in actions

[csharp]
/*
Program name: Hello World
Version:      1.0
Description:  Opens up a terminal and displays &quot;Hello World&quot;
*/

using System; // here we are importing the &quot;System&quot; namespace

// We are about to define the HelloWorld class.
class HelloWorld // This line is called the &quot;class declaration&quot; line
{         // curly brackets are used to structure the program, here we are indicating the start of the &quot;HelloWorld&quot; class.
   static void Main() // This is the &quot;method declaration&quot; line

     {    // Start of method's code block
        string message = &quot;Hello World&quot;;
        Console.WriteLine (message);
        Console.ReadLine();
     }  // End of method
} // end of class

[/csharp]