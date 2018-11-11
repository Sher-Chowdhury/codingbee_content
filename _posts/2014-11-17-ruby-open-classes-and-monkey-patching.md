---
ID: 184
post_title: 'Ruby &#8211; Open Classes and Monkey Patching'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-open-classes-and-monkey-patching
published: true
post_date: 2014-11-17 00:00:00
---
Let's say you have the following:

<pre>
class Human
  def greeting 
    puts "hello everybody"
  end
end

john = Human.new

john.greeting
</pre>

Next let's say you want to add a new method, called "hungry", then you can do this:


<pre>
class Human
  def greeting 
    puts "hello everybody"
  end

  def hungry 
    puts "I am hungry"
  end
end

john = Human.new

john.greeting
</pre>

However another approach is as follows:

<pre>
class Human
  def greeting 
    puts "hello everybody"
  end
end

john = Human.new

john.greeting

# the following line will fail, because you called before defining the "hungry" method. 
# john.hungry

class Human
  def hungry 
    puts "I am hungry"
  end
end

john.hungry
</pre>

Here we have extended the existing class with a new method. This was done by creating a follow up Human class block. This is possible because Ruby supports a concept known as "Open classes", which lets you do exactly this, i.e. extend an existing class, without altering the original class-block. 

"Monkey Patching" is something that's made possible thanks to the concept of open classes. "Monkey Patching" is the term used to describing extending a class's functionality (as shown above), or overwrite existing methods. 

Monkey Patching is especially useful for modifying code from third party vendors. 

Note if you wan to overwrite a private method, then the new method you have written needs to also be set top private. 

<pre>
class Human
  def greeting 
    puts "hello everybody"
  end
  
  def hungry 
    puts "I am hungry"
  end
end

john = Human.new

john.greeting

john.hungry

class Human
  def hungry 
    puts "I could eat a horse"
  end
end

john.hungry
</pre>

This will output:

<pre>
PS C:\Temp\irb> ruby .\openclasses.rb
hello everybody
I am hungry
I could eat a horse
PS C:\Temp\irb>
</pre>

You can even use this approach to modify the behaviour of the classes that are in the standard library, such as the string class.  For example:

<pre>
puts "hello".size

class String 
  def size
    puts "goodbye"
  end
end

puts "hello".size
</pre>

This outputs:

<pre>
PS C:\Temp\irb> ruby .\overwritingstandardlibrary.rb
5
goodbye
</pre>

Making changes in this way should be avoided because changing core functionality will confuse other people, and could stop othe ruby applications from working.