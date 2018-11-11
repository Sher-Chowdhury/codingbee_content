---
ID: 24
post_title: 'c# &#8211;  The &#8220;Main&#8221; method'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/c/c-the-main-method
published: true
post_date: 2014-05-10 00:00:00
---
In most case a c# file (aka .cs) will contain several (or hundreds of classes). After you have compiled it into an .exe file and double click on it. c# will need to know which part of the c# code needs to run first. You may some a code block in one of your classes that you want to run first when someone double clicks on the exe. But how do you identify this code block to c#? 

That's where the main method comes into play. In your cs file, you need to add a special method called the "Main" method to only one of your classes. The code in the main method will be executed someone double clicks on the exe file. The class that houses the main method can be thought of as the "primary class" or the "main class". The main method hence acts as your applications entry point. 



http://stackoverflow.com/questions/14688756/c-sharp-class-without-main-method

http://www.completecsharptutorial.com/basic/main-method.php