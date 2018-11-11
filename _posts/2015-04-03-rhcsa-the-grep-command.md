---
ID: 337
post_title: 'RHCSA &#8211; The grep command'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-the-grep-command
published: true
post_date: 2015-04-03 00:00:00
---
<h2>Overview of grep</h2>
By the end of this article you should be able to answer the following questions:

[expand title="Question"  alt=" "]
<pre>

</pre>
[/expand]
<br />
[expand title="Question" alt=" "]
<pre>

</pre>
[/expand]
<br />
[expand title="Question" alt=" "]
<pre>

</pre>
[/expand]
<br />
[expand title="Question" alt=" "]
<pre>

</pre>
[/expand]
<br />

<hr/>



<h2>What is grep?</h2> 
The grep command is for scanning through texts for a particular search term (which can be in the form of a regex string) and return all lines that meets the match. As a result grep is one of the most commonly used Linux commands. Grep essentially acts as a filtering tool that's used for only outputting the lines we are interested in.   

To use grep, all you have to do is give it some content and tell it what search-term/regular-expression to serach for. Grep will then output all lines where it finds a match. 

For example, let's say we have the following file:

<pre>
$ cat testfile.txt
A list of fruits:
apples
bananas
oranges
more bananas
</pre>

Now if we grep for bananas we get:

<pre>
$ grep 'bananas' testfile.txt
bananas
more bananas
</pre>


Or we can grep for the letter "e":
<pre>
$ grep 'e' testfile.txt
apples
oranges
more bananas
</pre>


We can also pipe content into the grep command, here's how we do this when grepping for "pp":

<pre>
$ cat testfile.txt | grep 'pp'     
apples

</pre>


If grep can't find a match, then it won't return anything at all:

<pre>
$ grep peaches testfile.txt
$
</pre>



If your search-term contains any spaces, then you need to enclose the search term in single or double quotes:

<pre>
$ grep "list of"
A list of fruits:
</pre>


Grep is commonly used as a way to filter out content from other commands, via piping, here's an example:

<pre>
$ ls -l | grep testfile
-rw-r--r--. 1 root root     0 May 24 14:23 testfile1.txt
-rw-r--r--. 1 root root     0 May 24 14:23 testfile2.txt

</pre>

However the true power of grep is unleashed when you grep to find matches using regular expressions.

<h2>Understanding Regular Expressions</h2>
Sometimes you will want to search for a pattern rather than a static string. This is done by using grep with a "regular expression" as search term rather than a simple string.

Combining the power of grep and regular expressions, gives you a far more intelligent matching capabilities. To do this, all you have to do use the reqular expression as the search term when executing the grep. You also have to enclose the regular expression in double quotes instead of single quotes.

Here are the most commonly used Regex Operators: 

[table id=3 /]

The best way to understand how grep and regular expressions works, you need to take a look at a few examples. Here are 3 examples of patterns you might want to search for:


<ul>
	<li>Example 1 - grep for all lines that starts with the letter d.</li>
	<li>Example 2 - grep for all lines that contains two digits, followed by ":" followed by 2 digits, followed by either "am" or "pm" (e.g. 08:30pm)</li>
	<li>Example 3 - grep for all lines that contain a 2 digits, followed by 3 letters, followed by 4 digits (e.g. 23Apr2013, or 14Nov2010</li>
</ul>


In Linux, you can represent these examples in the form of a special syntax called "regular expressions" (and then use grep to search for regular expressions, which is covered later). Here are the three examples written in the form of regular expression. Lets look at each example in turn.

<strong>Example 1 - Search for any lines that starts with the letter d</strong>

The corresponding regular expression is:
<pre>
"^d"</pre>

In the world of regular expressions, the carat symbol, "^" means "start with". If we omitted the carat then grep return lines that that contains the letter d anywhere on the line,  rather than just the beginning.


<strong>Example 2 - Search for any lines that contains two digits, followed by ":" followed by 2 digits, followed by either "am" or "pm" (e.g. 08:30pm)</strong>

The corresponding regular expression is:

<pre>"[0123456789][0123456789]:[0123456789][0123456789][ap]m"</pre>

Here, for the first digit, we used square brackets to encase all the digits that the first digit is allowed to be (the square brackets here are being used as special regular expression notations, are not being search for themselve). We did the same for the
second digit. The third character is the symbol ":". The next two are like the first two are the same as the first 2, followed by a character that can be either a or p, (as indicated by the encased square brackets). Finally the last character is just the letter "m".

Writing a regular expression this long can be a little tedious, but fortunately if the square brackets encases a sequential range of characters, then you can use a shorthand notation:

<pre>"[0-9][0-9]:[0-9][0-9][ap]m"</pre>

Here "-" is being as a special regular expression notation to represent a range, and won't be something that is searched for itself. The "-" only works when being used inside a square bracket.

This scenario hints that its purpose is to match for time stamps based on the 12 hour clock (although it doesn't say so explicitly). However if you were to use it for timestamp matching then it won't work because it can match invalid values, such as "99:99am". To overcome this issues, requires a bit more tweaking :

<pre>"[0-2][0-9]:[0-6][0-9][ap]m"</pre>


<strong>Example 3 - Search for any lines that contain a 2 digits, followed by 3 letters, followed by 4 digits (e.g. 23Apr2013, or 14Nov2010</strong>

The corresponding regular expression is:

<pre>"[0-9][0-9][A-Za-z][A-Za-z][A-Za-z][0-9][0-9][0-9][0-9]</pre>

The third character in this example is represented by [A-Za-z]. This means that it can be any upper case letter in the range A-Z, or lower case letter in the range a-z.

<strong>Example 3 - List all pictures in the current directory</strong>

The "$" match is used to match the end of the line rather than anywhere else.

<pre>$ ls -l
$ ls -l | grep ".jpg$"  
</pre>

For help info for regular expressions, checkout:

<pre>
$ man 7 regex
</pre>


<h2>Useful grep options</h2>

There's a bunch of option that you can use to control grep's default behaviour. You can view all of them using <code>grep --help</code>. Here are some useful options: 


<ul>
	<li><code> -i, --ignore-case</code>: find matches irrespective of upper/lower case characters</li>
	<li><code> -v, --invert-match</code>: This does a reverse match.</li>
	<li><code>-R, --dereference-recursive</code>: This recursively search through all content.</li>
</ul>




<h2>Wildcards and Regular expressions</h2>
At first sight, wild cards and grep appears to do the same thing. But there is a subtle difference. For example if you have a file called "rwx" then do ls -l to list this file. The wild card option will give the correct option whereas grep will confuse it with the first column of ls -l, which are the permission.



<h2>Linux Grep OR, Grep AND, Grep NOT Operator</h2>

http://www.thegeekstuff.com/2011/10/grep-or-and-not-operators/

Let's say we have the following file:


<pre>
$ cat testfile.txt
apple pie
apple cake
banofee cake
orange soda
brown bread
cottage pie
chocolate cake
white bread
upside down pineapple cake
bread and butter pudding
</pre>

Now let's say we want to grep for any lines containing the word "cake" or "pie". There's a few ways to do this. The first way is by using the "-E" option along with the grep's pipe operator:

<pre>
$ grep -E "cake|pie" testfile.txt
apple pie
apple cake
banofee cake
cottage pie
chocolate cake
upside down pineapple cake
</pre>

Or you can pass in multiple regex expressions using the -e option:

<pre>
grep -e "cake" -e "pie" testfile.txt
apple pie
apple cake
banofee cake
cottage pie
chocolate cake
upside down pineapple cake
</pre>


If you want to search for all lines contain "apple", we do:


<pre>
$ grep "apple" testfile.txt
apple pie
apple cake
upside down pineapple cake
</pre>

However if you want to grep for all lines containing "apple" AND "cake", then we can simply achieve this through piping:

<pre>
$ grep "apple" testfile.txt | grep "cake"
apple cake
upside down pineapple cake
</pre>





<h2>Recursively scan contents of all files for a regex match</h2>


This is possible with the -R option:

<pre>
grep -R search-term
</pre>

This will search all sub directories as well.