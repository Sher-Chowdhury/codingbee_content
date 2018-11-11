---
ID: 173
post_title: 'Ruby &#8211; Creating classes and objects'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-creating-classes-and-objects
published: true
post_date: 2014-11-12 00:00:00
---
To create a new class, you use the class keyword:

<pre>
class Employee
   # code goes here. 
end
</pre>

Previously we mentioned that variable names always start with a lower case. However in the case of classes, the convention is that class names always starts with an upper case, and it follows the CamelCase style. The convention also states that accronyms that are in uppercase too. Here's an example:

<pre>
class XMLReaderAndWriter
  # code goes here. 
end 
</pre>


In ruby you have a special keyword called "new" which is used to create an object from a class (i.e. instantiate a class), e.g.:


<pre>
NewObject = ClassName.new

</pre>

For, example:

<pre>
NewObject = Employee.new

</pre>


The "new" keyword will automatically trigger the class's "initiliaze" method. The initialize method is the equivalent to c#'s constructor method.