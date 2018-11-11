---
ID: 23
post_title: 'c# &#8211;  Input Parameters and Return Values'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/c/c-input-parameters-and-return-values
published: true
post_date: 2014-05-10 00:00:00
---
As mentioned earlier, a method is a block of code that performs a specific task.

Methods can require some information in order for it to run. This info has to be provided in the form of an "input parameter"

Methods can also output information after it has completed running. This info is often referred to as the  "return value",  and this is something that you can be captured into a variable. You can check whether a method requires an input parameter, or outputs a return value (or both) by looking at the  first line of the method's declaration.

For example, in an earlier lesson we encountered the WeekToDays method that not only requires an input parameter, but also outputs a return value:

[csharp]

class ConvertToDays
{
	static void Main()
	{
                int NumberOfDays = WeekToDays (4)
		System.Console.WriteLine (NumberOfDays);      // 28
	}

	static int WeekToDays (int Week)
	{
		int days = Week * 7;
		return days;
	}
}
[/csharp]

line xxx - What you will notice here is that when the Main method runs, it in turn invokes the WeekToDays method. The method requires an input parameter and we passed the input parameter of "4" using the round brackets. The WeekToDays then provides a return value after it finishes running, which is captured into a integer variable called "NumberOfDays".

[vision_notification style="neutral" font_size="12px" closeable="false"] Notice we didn't specify the full path of the WeekToDays method. That's because, the program always looks for the  method, in the class that triggers it first before looking elsewhere. [/vision_notification]

line 10 - There is a lot going on here, first off, it define's this method's name as "WeekToDays". The round brackets contains "int Week", this means that this method requires an integer (aka int) input parameter. After the method receives this integer, it assigns it to a variable called "Week" so that it can be used in the method's  code block. The "Int" just before the "WeekToDays" name, means that after this method finishes running, it returns an integer output parameter. You can identify what the method outputs by comes after the "return" statement (see line 12).

[vision_notification style="neutral" font_size="12px" closeable="false"] If a method is supposed to return an output-parameter then there must be a "return" command somewhere in the code, indicating what it is going to return.[/vision_notification]

So far, we haven't discussed what the "static" keyword means. But we will cover what Static means in a later lesson.

If we had a method, e.g. called "ProGeekTips" and  didn't require an input parameter, (but did output a string) then then the method's definition would look like this:
<pre>static string ProGeekTips ()</pre>
If this method didn't output a return value (but did require an array input parameter called "arg[]") then then the method's definition would look like this:
<pre>static void ProGeekTips (string[] arg1)</pre>
The "Void" keyword means that this method doesn't give out an output parameter.  Note, you can actually omit the Void key word, and c# will still assume that void is still implied:
<pre>static ProGeekTips (arg[])</pre>
If this method didn't have input parameters or return values, then the method's definition would simply look like this:
<pre>static void ProGeekTips ()</pre>
Going back to our ConvertToDays class, we can rewrite this into a more simpler form like this:

[csharp]

class sampleclass
{
	static void Main()
	{
                int NumberOfDays = WeekToDays (4)
		System.Console.WriteLine (NumberOfDays);      // 28
	}

	static int WeekToDays (int Week)
	{
		return Week * 7;
	}
}
[/csharp]