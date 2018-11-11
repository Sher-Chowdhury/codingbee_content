---
ID: 177
post_title: 'Ruby &#8211; Use &#8220;super&#8221; to extend inherited methods'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-use-super-to-extend-inherited-methods
published: true
post_date: 2014-11-14 00:00:00
---
If you want a child class, which has inherited methods, but you dont want to fully replace the method, instead you want to either prepend/append some code to the method's code, then you can do so using the "super" keyword, like this:

<pre>
# here's the parent class. 
class Animal 
  def quack
    puts "Here is a statement about the quack noise:"
  end

  def feathers
    puts "The above line is a statement relating to feathers:"
  end  
end

# Here's the child class. 
class Duck   < Animal
  def quack
    super
	puts "Quaaaaaack!"
  end

  def feathers
    puts "The duck has white and gray feathers."
	super
  end  

end

donald = Duck.new

donald.quack
donald.feathers

</pre>


this outputs:

<pre>
PS C:\Temp\irb> ruby .\inhertancet.rb
Here is a statement about the quack noise:
Quaaaaaack!
The duck has white and gray feathers.
The above line is a statement relating to feathers:
PS C:\Temp\irb>
</pre>




If the method that is being "supered" requires input variables, then they need to be passed in as part of the corresponding super keyword:



<pre>
# here's the parent class. 
class Animal 
  def quack(message1,message2)
    puts "the message is #{message1} and #{message2}"
  end

  def feathers
    puts "The above line is a statement relating to feathers:"
  end
  
  
end

# Here's the child class. 
class Duck   < Animal
  def quack
    super("duckling1","duckling2")
	puts "Quaaaaaack!"
  end

  def feathers
    puts "The duck has white and gray feathers."
	super
  end  

end
 

donald = Duck.new


donald.quack
donald.feathers
</pre>

This outputs:

<pre>
PS C:\Temp\irb> ruby .\inhertancet.rb
the message is duckling1 and duckling2
Quaaaaaack!
The duck has white and gray feathers.
The above line is a statement relating to feathers:
PS C:\Temp\irb>
</pre>