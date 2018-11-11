---
ID: 11
post_title: 'c# &#8211; Hello World'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/c/c-hello-world
published: true
post_date: 2014-05-10 00:00:00
---
Here we are going to first show you a simple "hello world" program in the form of an exe file. When you double-click on the exe file, all that will happen is that a console terminal will open a display the message "hello world", like this:

&nbsp;
<h3><strong><span style="color: #008000;">Step 1 -Write the code</span> </strong></h3>
Open notepad and write:

[csharp]

using System;
class HelloWorld
{
   static void Main()         [content_tooltip id=&quot;650&quot;]
     {
        string message = &quot;Hello World&quot;;
        Console.WriteLine (message);
        Console.ReadLine();
     }
}

[/csharp]

[vision_notification style="warning" font_size="12px" closeable="false"] In c#, everything is case sensitve. That means if you type "using system;" instead of typing "using System;" then you will get an error message. [/vision_notification]

[vision_notification style="neutral" font_size="12px" closeable="false"] Every c# statement must end with a semi-colon. [/vision_notification]

<strong>line 1 -</strong> Here we are importing a "namespace" called "System". A namespace is essentially a collection of "classes" that we want to make use of in our program. A class is basically a collection of blocks of code, of which, some of these blocks are known as "methods". We will cover Classes, Namespaces, and Methods, in more detail in later lessons. We are loading the System namespace into memory here because, it contains a class called "Console" which we will be using further down in the code.

<strong>line 2 - </strong>Here we are declaring a class, and we chose to call it "HelloWorld". In this class we have defined only one method, which we have called "Main". You can give a method any name you like, however calling a method "Main" has special meaning in c#. We will cover methods (including the Main method) in later lessons.

<strong>line 4 - </strong>Here we are defining a method called "Main". The keywords "static" and "void" defines some of the characteristics of this method. We will cover this in more detail in later lessons.

<strong>Line 6</strong> - Here we are declaring a string variable called "message".

<strong>Line 7</strong> - Here we are making use of a method called "WriteLine" which resides in a class called "Console", which in turn resides in the "System" namespace. The writeline method's purpose, as the name indicates, is to write something onto a console. I have instructed writeline what to display by placing the "message" variable into the round-brackets. If I didn't place anything in the round brackets, then I would get an error message because the writeline method requires a parameter input for it to work.

<strong>Line 8</strong> - Here we are using another method that belongs to the Console class, this method is called ReadLine. We are using this method to keep the console window open. If I try to run the program without including this line, then the terminal will pop up for a fraction of a second, and then closes again. This method forces the terminal to stay open.
<h3><span style="color: #339966;"><strong>Step 2</strong> -Save the code</span></h3>
Save the file as a "cs" file:

<a href="http://progeektips.com/wp-content/uploads/2014/01/2014-01-18-18_38_51-_new-2-Notepad++.png"><img class="aligncenter size-full wp-image-101" src="http://progeektips.com/wp-content/uploads/2014/01/2014-01-18-18_38_51-_new-2-Notepad++.png" alt="2014-01-18 18_38_51-_new  2 - Notepad++" width="710" height="213" /></a>
<h3><span style="color: #339966;"><strong>Step 3</strong> - Locate the compiler</span></h3>
Next we generate an executable from this cs file (aka we are going to compile the code). Open up cmd (as admin), and then navigate to the latest version of your .net framework's directory.

<a href="http://progeektips.com/wp-content/uploads/2014/01/2014-01-18-18_32_27-Program-Manager.png"><img class="aligncenter size-full wp-image-100" src="http://progeektips.com/wp-content/uploads/2014/01/2014-01-18-18_32_27-Program-Manager.png" alt="2014-01-18 18_32_27-Program Manager" width="854" height="561" /></a>
<h2><span style="color: #008000;">Step 4 - Compile the code</span></h2>
In this directory, you should find the csharp compiler, which is called "csc.exe". Now run the csc.exe (passing in the path to your helloworld csharp file as a parameter):

<a href="http://progeektips.com/wp-content/uploads/2014/01/2014-01-18-18_44_23-Program-Manager.png"><img class="aligncenter size-full wp-image-103" src="http://progeektips.com/wp-content/uploads/2014/01/2014-01-18-18_44_23-Program-Manager.png" alt="2014-01-18 18_44_23-Program Manager" width="854" height="561" /></a>

This will create the exe file which you will find in the .net folder.

Note: Your compiled program ends up in the folder where your terminal has been cd into. If you want your programs to be automatically placed in another folder, e.g. c:\, then first go to that directory and then run the compiler by fully stating it's path:

<a href="http://progeektips.com/wp-content/uploads/2014/01/2014-01-18-19_11_53-Administrator_-Command-Prompt.png"><img class="aligncenter size-full wp-image-105" src="http://progeektips.com/wp-content/uploads/2014/01/2014-01-18-19_11_53-Administrator_-Command-Prompt.png" alt="2014-01-18 19_11_53-Administrator_ Command Prompt" width="854" height="560" /></a>
<h3><span style="color: #008000;">Step 5 - Locate the Program
</span></h3>
Open the folder where the exe has been saved to (this is the directory that your terminal is in at the time of running the csc.exe).

<a href="http://progeektips.com/wp-content/uploads/2014/01/2014-01-18-19_18_01-temp.png"><img class="aligncenter size-full wp-image-106" src="http://progeektips.com/wp-content/uploads/2014/01/2014-01-18-19_18_01-temp.png" alt="2014-01-18 19_18_01-temp" width="1039" height="565" /></a>

Tip: Just type "explorer ." this will open up in explorer window in the terminal in the current directory (which is denoted by ".").
<h3><span style="color: #008000;"><strong>Step 6 - Run the Hello World program</strong></span></h3>
Double click the exe to run it:

<a href="http://progeektips.com/wp-content/uploads/2014/01/2014-01-18-19_19_31-C__temp_helloworld.exe_.png"><img class="aligncenter size-full wp-image-107" src="http://progeektips.com/wp-content/uploads/2014/01/2014-01-18-19_19_31-C__temp_helloworld.exe_.png" alt="2014-01-18 19_19_31-C__temp_helloworld.exe" width="852" height="559" /></a>

Here's another example csharp program, but this program is doing a bit of maths:

[csharp]
using System;

class SimpleMaths
{
    static void Main()
    {
        int x = 12 * 30;
        Console.WriteLine (&quot;Twelve times thirty is equal to: &quot; + x);
        Console.ReadLine();
    }
}
[/csharp]

The compiled exe outputs:

&nbsp;