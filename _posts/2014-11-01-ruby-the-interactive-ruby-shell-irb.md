---
ID: 167
post_title: 'Ruby &#8211; The Interactive Ruby Shell (IRB)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-the-interactive-ruby-shell-irb
published: true
post_date: 2014-11-01 00:00:00
---
Most things in ruby are objects. Even classes themselves are objects!

You can run ruby commands straight from the command line. First open up Powershell and then run "irb" to enter the interactive ruby shell

<pre>
<span style="font-size:10px">PS C:\> irb
irb(main):007:0> a_string = "Hello World!"
=> "Hello World!"
irb(main):008:0> a_string.class  # We used a method called "class" to output data type.
=> String
irb(main):009:0> an_integer = 7   # here we create a new variable called "an_integer"
=> 7
irb(main):010:0> an_integer.class
=> Fixnum
irb(main):011:0> a_big_integer = 76464685413123454835455225
=> 76464685413123454835455225
irb(main):012:0> a_big_integer.class
=> Bignum
irb(main):013:0>
</span>
</pre>


As you can see "a_string" variable is actually an instance of the <a href="http://www.ruby-doc.org/core-2.1.4/String.html">"String" class</a>.

In the above example we only tried applying only one of the string class's <strong>instance method</strong>, called "class". The "class" method outputs the name of the class that this object has been instantiated from. 

If you want to see what other instance methods are available that you can apply to this type of object, then you can do this by using another <strong>instance method</strong> called "public_methods":

<pre>
irb(main):001:0> a_string = "hello world"
=> "hello world"
irb(main):002:0> a_string.public_methods
=> [:<=>, :==, :===, :eql?, :hash, :casecmp, :+, :*, :%, :[], :[]=, :insert, :length, :size, :bytesize, :empty?, :=~, :match, :succ, :succ!, :next, :next!, :upto, :index, :rindex,:replace, :clear, :chr, :getbyte, :setbyte, :byteslice, :to_i, :to_f, :to_s, :to_str, :inspect, :dump, :upcase, :downcase, :capitalize, :swapcase, :upcase!, :downcase!, :capitalize!, :swapcase!, :hex, :oct, :split, :lines, 
.
.
...etc
</pre>

Use the "sort" method to make it easier to read, by sorting output into alpahbetical order:

<pre>
irb(main):074:0* a_string.public_methods.sort
=> [:!, :!=, :!~, :%, :*, :+, :<, :<<, :<=, :<=>, :==, :===, :=~, :>, :>=, :[], :[]=, :__id__, :__send__, :ascii_only?, :between?, :bytes, :bytesize, :byteslice, :capitalize, :capitalize!, :casecmp, :center, :chars, :chomp, :chomp!, :chop, :chop!, :chr, :class, :clear,:clone, :codepoints, :concat, :count, :crypt, :define_singleton_method, :delete, :delete!, :display, :downcase, :downcase!, :dump, 
.
.
... etc
</pre>

To make the above even more easier to read, then prefix the "puts" command:


<pre>
irb(main):007:0> <strong>puts</strong> a_string.public_methods.sort
!
!=
!~
%
*
+
<
<<
<=
<=>
==
===
=~
>
>=
[]
[]=
__id__
__send__
ascii_only?
between?
bytes
bytesize
byteslice
capitalize
capitalize!
casecmp
center
chars
chomp
chomp!
chop
chop!
.
.
...etc.
</pre>

Note: the "public_methods" method is similar to Powershell's "get-member" command. 

When you call the "class" method, the irb not only outputs the class's name that the object has been instantiated from, but output's the object itself. <strong>That's becuase classes are actually objects themselves!</strong>. Since classes are also objects, it means objects (that represent classes) has it's own set of methods:

<pre>
irb(main):031:0* a_string = "hello world"
=> "hello world"
irb(main):032:0> a_string.class
=> String
irb(main):026:0* puts a_string.class.public_methods.sort
!
!=
!~
<
<=
<=>
==
===
=~
>
>=
__id__
__send__
allocate
ancestors
autoload
autoload?
class
class_eval
class_exec
class_variable_defined?
.
.
...etc.
</pre>
 
Now to find out what class the "String" class has been instantiated from (i.e. what is the String class's parent class), this time you use the "superclass" method (for some reason using the class method again doesn't work):

<pre>
irb(main):255:0> a_string.class.superclass
=> Object
</pre>


Once again, "Object" is not only a class, but also an object in it's own right. Therefore let's see what methods can be applied to this "class-object":


<pre>
irb(main):037:0* puts a_string.class.superclass.public_methods.sort
!
!=
!~
<
<=
<=>
==
===
=~
>
>=
__id__
__send__
allocate
ancestors
autoload
autoload?
class
class_eval
class_exec
.
.
...etc
</pre>



Now let's see which class the "Object" instance has been instantiated from: 


<pre>
irb(main):257:0> a_string.class.superclass.superclass
=> BasicObject
</pre>

Once again, "Object" is not only a class, but also an object in it's own right. Therefore let's see what methods can be applied to this "class-object":

<pre>
puts a_string.class.superclass.superclass.public_methods.sort
!
!=
!~
<
<=
<=>
==
===
=~
>
>=
__id__
__send__
allocate
ancestors
autoload
autoload?
class
class_eval
class_exec
class_variable_defined?
class_variable_get
.
.
.
=> nil
</pre>

Note, nil is  the top level class, that all other class-objects are derived from. 

<pre>
irb(main):217:0* puts a_string.class.superclass.superclass.superclass.public_methods.sort
!
!=
!~
&
<=>
==
===
=~
^
__id__
__send__
class
clone
define_singleton_method
display
.
.
.
=> nil
</pre>


You don't need to create a variable before inspecting it's methods, you can apply methods to any objects directly:

<pre>
irb(main):298:0* "hello world".upcase
=> "HELLO WORLD"
irb(main):299:0> "hello world".class
=> String
</pre>

You can also define your methods straight in the command line, for example here is a method: 

<pre>
irb(main):300:0> def doublingnumber(val); val * 2; end
=> nil
irb(main):301:0> doublingnumber(6)
=> 12
irb(main):302:0>

# Notice that it also doubles strings in an odd way:
irb(main):304:0> doublingnumber("hello")
=> "hellohello"

# Also here's what happens when you pass in an array:
irb(main):305:0> doublingnumber([1,2,3])
=> [1, 2, 3, 1, 2, 3]

# If you want to retrieve the last output from the doublingnumber method, then 
# you can simply use the following shortcut:

irb(main):315:0> doublingnumber(6)
=> 12
irb(main):316:0> _              # Notice that we simply used a single underscore!
=> 12
</pre>