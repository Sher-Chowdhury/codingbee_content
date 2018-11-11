---
ID: 20
post_title: 'c# &#8211;  What are Variables Types'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/c/c-what-are-variables-types
published: true
post_date: 2014-05-10 00:00:00
---
A variable is essentially a container that's used for storing data. This container has a name assigned to it. In programming, this name is used to refer to the variable's content.

In c# there are actually three high-level types of variables:
<ul>
	<li>Value Types</li>
	<li>Reference types</li>
	<li>Pointer types   (we will ignore this for now, but will cover it later)</li>
</ul>
<h2>Value Types</h2>
The following variable types fall under this category:
<table>
<tbody>
<tr>
<th>Type</th>
<th>Represents</th>
<th>Range</th>
<th>Default
Value</th>
</tr>
<tr>
<td>bool</td>
<td>Boolean value</td>
<td>True or False</td>
<td>False</td>
</tr>
<tr>
<td>byte</td>
<td>8-bit unsigned integer</td>
<td>0 to 255</td>
<td>0</td>
</tr>
<tr>
<td>char</td>
<td>16-bit Unicode character</td>
<td>U +0000 to U +ffff</td>
<td>'\0'</td>
</tr>
<tr>
<td>decimal</td>
<td>128-bit precise decimal values with 28-29 significant digits</td>
<td>(-7.9 x 10<sup>28</sup> to 7.9 x 10<sup>28</sup>) / 10<sup>0 to 28</sup></td>
<td>0.0M</td>
</tr>
<tr>
<td>double</td>
<td>64-bit double-precision floating point type</td>
<td>(+/-)5.0 x 10<sup>-324</sup> to (+/-)1.7 x 10<sup>308</sup></td>
<td>0.0D</td>
</tr>
<tr>
<td>float</td>
<td>32-bit single-precision floating point type</td>
<td>-3.4 x 10<sup>38</sup> to + 3.4 x 10<sup>38</sup></td>
<td>0.0F</td>
</tr>
<tr>
<td>int</td>
<td>32-bit signed integer type</td>
<td>-2,147,483,648 to 2,147,483,647</td>
<td>0</td>
</tr>
<tr>
<td>long</td>
<td>64-bit signed integer type</td>
<td>-923,372,036,854,775,808 to 9,223,372,036,854,775,807</td>
<td>0L</td>
</tr>
<tr>
<td>sbyte</td>
<td>8-bit signed integer type</td>
<td>-128 to 127</td>
<td>0</td>
</tr>
<tr>
<td>short</td>
<td>16-bit signed integer type</td>
<td>-32,768 to 32,767</td>
<td>0</td>
</tr>
<tr>
<td>uint</td>
<td>32-bit unsigned integer type</td>
<td>0 to 4,294,967,295</td>
<td>0</td>
</tr>
<tr>
<td>ulong</td>
<td>64-bit unsigned integer type</td>
<td>0 to 18,446,744,073,709,551,615</td>
<td>0</td>
</tr>
<tr>
<td>ushort</td>
<td>16-bit unsigned integer type</td>
<td>0 to 65,535</td>
<td>0</td>
</tr>
</tbody>
</table>
<h2>Reference Types</h2>
The following variable types are categorized under reference types:
<ul>
	<li>string</li>
	<li>object</li>
	<li>dynamic</li>
</ul>
<h1>Value Types verses Reference Types</h1>
So far we have listed all the variable types. But why is there a need to pigeonhole  into value types or reference types?

The main reason boils down to memory usage.

In value type variables, each variable's value is only accessible by one variable name. Whereas in reference types variables, each variable's value can be accessible be one or more variable in order to save space.

To be better understand this, let's take a look at an example:

Let's say later on we declare the following (value type) integer variable:

int MyNumber1 = 12025 ;

This variable will take up about 32Bytes of your computer's memory. This isn't a lot of memory since most modern computers, comes with 2GB of RAM (but don't forget about 1.5GB of your ram is already used up to run your operating system).

Now lets say further along in the program we create  a second variable like this:

int MyNumber2 = MyNumber1 ;

We have affectively made a copy of our first variable. As a result, another 32Bytes of memory is used up in order to store the value for MyNumber2.

Now let's take a look at what happens when we we deal with reference type variables.  Let's say somewhere in our code we declare the following (reference type) string variable, and this variable we are going to store the whole the entire text of the "Lord of the Rings" trilogy:

string LordOfTheRings = "blah blah blah.................
.......................hobbits....blah blah........legolas...blah blah.....................
.................................................................................................etc";

I don't know exactly how much memory a string this big would use up, but lets say it is something really big, like 50MB of memory.

Now lets say later on we declare a second string variable like this:

string MyFavouriteBook = LordOfTheRings;

In doing this you would think that another massive 50MB of memory gets used up to store the value of MyFavouriteBook.

If that were true then this would be bad because our program would end up hogging all the computer's memory and will either cause our PC to crash or really slow  down in performance.

However what really happens is that "MyFavouriteBook" basically becomes another reference to the same memory location that holds the value

Another way you can think of this is to think of a memory location as a large empty room with no doors or windows. When we created the LordOfTheRings variable, we basically created a door to give access to the room, and in the room we placed the LordoftheRings book. The door's nameplate reads the variable's name, i.e. "LordofTherings". So when the following code was run:

string MyFavouriteBook = LordOfTheRings;

All we did was create another doorway into the same room, but this time the new door's nameplate reads "MyFavouriteBook".

Hence both LordofTherings and MyFavouriteBook acts as reference to access the same room (memory location). This is only done to make efficient use of the memory.

However if I want to change the value of MyFavouriteBook to something like the entire text of the Harry Potter books, i.e.:

string MyFavouriteBook = "blah blah blah.................
....Voldemort....blah blah........Professor Dumbledore...blah blah......................................................................................................................etc";

then the MyFavouriteBook can't point to the LordOftheRings data anymore. It also can't overwrite the LordoftheRings, because the other variable, LordOfTheRings, might still need it. In this scenario, there is no other option but to place the data into a new memory location, in other words we place the new data in a different room, and then move the  MyFavouriteBook "door" from it's old place and place it as entry point to the new room.

If the Harry Potter texts use up 100MB, then to store both string variables would end up using a massive 150MB of memory.

Here is an example that shows that when you have to variables pointing to the same memory location, and then you change the value of one of variables, then it doesn't impact the other variable:

[csharp]

using System;
static class StringaAreReferenceTypes
{
static void Main()
{
string x = &quot;hello ProGeekTips&quot;;
Console.WriteLine (x);

string y = x;
Console.WriteLine (y);

x = &quot;goodbye ProGeekTips&quot;;
Console.WriteLine (x);  // This now outputs &quot;goodbye ProGeekTips&quot;
Console.WriteLine (y);    // This still outputs &quot;hello ProGeekTips&quot;

}
}

[/csharp]

Now with value types, you can't have 2 variables names (doorways) leading into the same memory location (room). Also value type gets a fixed size container to store it's data in. For example, the size of an integer (int) variable's memory 32bytes, that's big enough to store any integer between -2,147,483,648 to 2,147,483,647. If you want to store a variable that is even bigger, something bigger than you have to use another value type instead, e.g. a float variable type.

The key thing to an

In c# the re are different types of variables. These different types are known as <a href="http://msdn.microsoft.com/en-us/library/s1ax56ch.aspx" target="_blank">Value Types</a> (aka data types) some of the most common ones are :
<ul>
	<li>string</li>
	<li>int</li>
	<li>array</li>
	<li>float</li>
	<li>boolean</li>
</ul>
Lets take a look at each of them in practice:
<h2>String Data type</h2>
We have already encountered a string variable in our earlier hello world program:

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

Line 8 - here we are creating a new string variable called "message", and this variable contains the data "Hello World".

<strong>Useful Links</strong>

http://msdn.microsoft.com/en-us/library/s6938f28.aspx

http://msdn.microsoft.com/en-us/library/3ewxz6et.aspx