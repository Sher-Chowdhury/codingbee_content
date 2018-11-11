---
ID: 185
post_title: 'Ruby &#8211; Checking whether 2 objects are equal'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-checking-whether-2-objects-are-equal
published: true
post_date: 2014-11-21 00:00:00
---
By definition 2 objects cannot be identical (even if they are instantiated from the same class). If that were not the case, then that would defeat the whole point of OOP. 

<pre>
class Human
  attr_reader :name
 
  def initialize(firstname) 
    @name = firstname
  end 

  def greeting 
    puts "hello everybody"
  end
  
  def hungry 
    puts "I am hungry"
  end
end

john = Human.new("Jonathon")
puts john.name
puts john.object_id

john = Human.new("Jonathon")
puts john.name
puts john.object_id
</pre>

This outputs something like:

<pre>
Jonathon
18214200
Jonathon
21469788
</pre>

As you can see, these are 2 different objects. In fact since we have used the same name for the object, the second instantiation replaced the first instantiation. 

However if we did give each object a different label, e.g.:

<pre>
class Human
  attr_reader :name
 
  def initialize(firstname) 
    @name = firstname
  end 

  def greeting 
    puts "hello everybody"
  end
  
  def hungry 
    puts "I am hungry"
  end
end

john1 = Human.new("Jonathon")
puts john1.name
puts john1.object_id

john2 = Human.new("Jonathon")
puts john2.name
puts john2.object_id
</pre>

Then the output still shows something like:

<pre>
PS C:\temp\irb> ruby .\comparing2objects.rb
Jonathon
18083124
Jonathon
21338700
PS C:\temp\irb>
</pre>

So while we may recognise these 2 objects as actually referring to the same real-life object. In Ruby they are viewed as two separate objects. Just to confirm this is the case, you can also do:

<pre>
puts john1.equal?(john2)  # this will output false. 
</pre>

So the best way to determine whether two object are actually referring to the same real life object, you first need to determine what attribute must match. In the above case, there is just one attribute, called "name", so to determine, if they are referring to the same real life object, then you can do:

<pre>
john1.name == john2.name   # which will output true. 
</pre>