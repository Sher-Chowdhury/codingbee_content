---
ID: 175
post_title: 'Ruby &#8211; Duck Typing'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-duck-typing
published: true
post_date: 2014-11-14 00:00:00
---
Duck Typing is a bit like an alternative concept to the inheritance concept. It basically let's you create a standalone method, that resides outside a class. Another way to look at it is that duck typing is a bit like creating your very own "static method".

Before we start explaining what "duck typing" is, let's first take a look at an example of inheritance in action:

<pre>
class Animal 
  def quack
    puts "unknown animal that makes a quack noise"
  end

  def feathers
    puts "this animal has something to do with feathers"
  end
  
  # Here we have taken the slightly unusual step of passing the object into the method. 
  # a better approach would be to not pass anything in, and just use the "self" keyword instead. 
  def in_the_forest(an_object)
    an_object.quack
    an_object.feathers
  end 
  
end


class Duck   < Animal
  def quack
    puts "Quaaaaaack!"
  end

  def feathers
    puts "The duck has white and gray feathers."
  end  

end
 

class Person   < Animal
  def quack
    puts "The person imitates a duck."
  end
  
  def feathers
    puts "The person takes a feather from the ground and shows it."
  end
end
 

 

donald = Duck.new
john = Person.new
mystery_animal = Animal.new

# If we used the "self" keyword earlier on then we wouldn't have needed to pass in the object itself in again. 
donald.in_the_forest(donald)
john.in_the_forest(john)
mystery_animal.in_the_forest(mystery_animal)
</pre>

Here we have taken a slightly odd approach in the way we have defined the in_the_forest method. This will make better sense later on. Also notice that both the quack and feathers method has been over-ridden, and only in_the_forest method hasn't been overridden in the child classes. 



This outputs:

<pre>
PS C:\Temp\irb> ruby .\inhertance.rb
Quaaaaaack!
The duck has white and gray feathers.
The person imitates a duck.
The person takes a feather from the ground and shows it.
unknown animal that makes a quack noise
this animal has something to do with feathers
PS C:\Temp\irb>
</pre>

Now let's say that we actually want to instantiate the Animal class. Also the quack and feathers classes will always be redefined in all of Animals child class, then we can in theory refactor the code to:


<pre>
class Animal 
  
  def in_the_forest(an_object)
    an_object.quack
    an_object.feathers
  end 
  
end


class Duck   < Animal
  def quack
    puts "Quaaaaaack!"
  end

  def feathers
    puts "The duck has white and gray feathers."
  end  

end
 

class Person   < Animal
  def quack
    puts "The person imitates a duck."
  end
  
  def feathers
    puts "The person takes a feather from the ground and shows it."
  end
end
 


donald = Duck.new
john = Person.new

donald.in_the_forest(donald)
john.in_the_forest(john)
</pre>

This outputs:

<pre>
PS C:\Temp\irb> ruby .\inhertancet.rb
Quaaaaaack!
The duck has white and gray feathers.
The person imitates a duck.
The person takes a feather from the ground and shows it.
PS C:\Temp\irb>
</pre>


At this poinnt, the (parent) Animal class only contains one method. Having a parent that only contains one method, can be thaught of as a bit overkill. It could be argued that it would be better to get rid of the Animal class altogether and have the in_the_forest method as a standalone method. This is possible like this:

<pre>
def in_the_forest(an_object)
  an_object.quack
  an_object.feathers
end 


class Duck   
  def quack
    puts "Quaaaaaack!"
  end

  def feathers
    puts "The duck has white and gray feathers."
  end  

end
 

class Person 
  def quack
    puts "The person imitates a duck."
  end
  
  def feathers
    puts "The person takes a feather from the ground and shows it."
  end
end
 

donald = Duck.new
john = Person.new

in_the_forest(donald)
in_the_forest(john)

</pre>

This outputs:

<pre>
PS C:\Temp\irb> ruby .\inhertancet.rb
Quaaaaaack!
The duck has white and gray feathers.
The person imitates a duck.
The person takes a feather from the ground and shows it.
PS C:\Temp\irb>
</pre>

Here, you can use the in_the_forest method for any objects whose corresponding class contains the quack and feathers method.

If we head originally wrote the in_the_forest method using the "self" syntax then this wouldn't have been possible and the in_the_forest method would have needed to be rewritten to the way it is written, i.e. requiring an object as in input parameter. 

As mentioned there are few cases where you might need to use ducktyping.   


The key benefit of duck typing is that it lets you avoid writing unnecessary classes just to hold generic methods.