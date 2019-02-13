---
ID: 59
post_title: c-adding-comments-to-your-code
author: admin
post_excerpt: ""
layout: post
permalink: http://localhost:8000/?p=59
published: true
post_date: 2014-05-10 00:00:00
---
It is always good practice to insert comments into your code so that you (as well as others) can understand what your code is doing.

You can place a single line comment after a line of code, or on a line of it's own using the "//" notation.

If you want to insert a multi-line comment, then you can begin it using /* and end the comment with */.

Here we the 2 comments types in actions

[csharp] /* Program name: Hello World Version: 1.0 Description: Opens up a terminal and displays "Hello World" */

using System; // here we are importing the "System" namespace

// We are about to define the HelloWorld class. class HelloWorld // This line is called the "class declaration" line { // curly brackets are used to structure the program, here we are indicating the start of the "HelloWorld" class. static void Main() // This is the "method declaration" line

     {    // Start of method's code block
        string message = &quot;Hello World&quot;;
        Console.WriteLine (message);
        Console.ReadLine();
     }  // End of method

} // end of class

[/csharp]