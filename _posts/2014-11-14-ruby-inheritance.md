---
ID: 176
post_title: 'Ruby &#8211; Inheritance'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-inheritance
published: true
post_date: 2014-11-14 00:00:00
---
Here's an example of inheritance in action:


<pre>
# Here we define a parent class
class Animal 
  def quack
    puts "unknown animal that makes a quack noise"
  end

  def legs
    puts "this animal has a pair of legs"
  end
  
end

# Here we define a child class of the animal class. In this case we 
# have over-ridden the "quack" method. But it implicitly inherits the "legs" method as-is. 
class Duck   < Animal
  def quack
    puts "Quaaaaaack!"
  end
 

end
 
 
# Here we define another child class of the animal class. In this case we 
# have over-ridden the "quack" method. But it implicitly inherits the "legs" method as-is.  
class Person   < Animal
  def quack
    puts "The person imitates a duck."
  end
end
 

donald = Duck.new
john = Person.new

# the child class's method should be run in both these instances. 
donald.quack
john.quack
 
# The parent class's method should end up being run in these two instances. 
donald.legs
john.legs


mystery_animal = Animal.new
mystery_animal.quack
mystery_animal.legs
</pre>


The above outputs:

<pre>
PS C:\Temp\irb> ruby .\inhertancet.rb
Quaaaaaack!
The person imitates a duck.
this animal has a pair of legs
this animal has a pair of legs
unknown animal that makes a quack noise
this animal has a pair of legs
</pre>